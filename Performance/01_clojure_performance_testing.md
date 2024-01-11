# Learning Clojure performance testing

https://github.com/ThePrimeagen/BunSpreader

Following this repo as the example I tried to create a Clojure version of the server with the intent to see how it performs compared to the other langauges.

## Issues

(atom {}) does not deal well with long swap operations. My existing code treated the global-q as a vector. With new elements being `conj` on and elemnets removed via `(swap! global-q empty-queue)`. However with 100 parallel connections what I was seeing was that the first 130,000 entries were added near instantly, but after that it essentially froze. The `empty-queue` routine was so slow that it was being restarted every time something new was added.

Since `swap!` will run the fastest operation and force everything else to restart.

The interesting part is that `(rest @global-q)` was taking multiple seconds to process. 

Something else weird is that it never really stopped adding values. I'm not really sure why.

## How Do I Fix This?

https://github.com/clojure-goes-fast/clj-async-profiler/

Let's performance test!

To get this working needed to add some :jvm-opts as a vector to my project.clj 

```clj
(defproject clj_bun_spreader "0.1.0-SNAPSHOT"
  :description "Clojure server for comparing against other languages."
  :url "http://github.com/dawguy/blog"
  :license {:name "EPL-2.0 OR GPL-2.0-or-later WITH Classpath-exception-2.0"
            :url "https://www.eclipse.org/legal/epl-2.0/"}
  :dependencies [[org.clojure/clojure "1.11.1"]
                 [ring/ring-core "1.9.6"]
                 [ring/ring-json "0.5.1"]
                 [ring/ring-jetty-adapter "1.9.6"]
                 [compojure "1.6.3"]
                 [cheshire "5.11.0"]
                 [metosin/reitit "0.7.0-alpha5"]
                 [metosin/reitit-ring "0.7.0-alpha5"]
                 [metosin/malli "0.11.0"]
                 [org.clojure/tools.logging "1.2.4"]
                 [nrepl "1.0.0"]
                 [com.clojure-goes-fast/clj-async-profiler "1.0.3"]
                 ]
  :jvm-opts ["-Djdk.attach.allowAttachSelf" "-XX:+UnlockDiagnosticVMOptions" "-XX:+DebugNonSafepoints"]
  :repl-options {:init-ns clj-bun-spreader.core}
  :main clj-bun-spreader.core)

```

These are the new functions I added to help test.

```clojure
(defn fill-global-queue [n]
  (reset! global-q [])
  (dotimes [i n]
    (swap! global-q conj {:time (.minusSeconds (java.time.Instant/now) 15) :body {:a i}})
))

(comment ""
   (fill-global-queue 100)
   (fill-global-queue 1000)
   (fill-global-queue 10000)
   (fill-global-queue 100000)
   (fill-global-queue 200000)

   (prof/profile (empty-queue! @global-q))
   (prof/serve-ui 8080)
  .)
```

## Conclusions From This First Pass Of The Graphs

I have no freaking clue how Clojure works. It feels like voodoo magic that I can't quite wrap my head around now. Pretty much the whole resulting flame graph is made up of java.* methods that I didn't explicitly use.

One part that should be pretty important I think is that `java.lang.reflect.Method.copy` is used in 35% of the samples. Which makes me think that there is a lot of copying going on in the background.

```clj
(prof/profile (dotimes [i 5000000] (rest @global-q)))
```

This graph makes it look like there are a couple layers of reading an atom.

## What to try next

Since I can't figure out why the copying is happening, let's try doing this with a Head and Tail node instead of vector.

# Second Pass. Months later

Ends up I had a bug that made this not work as expected due to the types of the lists swapping between `vec` and `seq`.

The fill-global-queue func didn't hit this bug because the type of the list was changed by the dequeue func.

```clj
(first (seq [1 2 3]) -- 1
(first (vec [1 2 3]) -- 1

(conj (vec [1 2 3]) 4) -- Ends up with [1 2 3 4]
(conj (seq [1 2 3]) 4) -- Ends up with (4 1 2 3)
```

So essentially my expectation was that we'd always be doing a FIFO queue.
But after doing a remove, it swapped to FILO queue, making the remove logic look like (first @list) always sees a near max timout remaining element.

Swapping this around so it would treat everything as a vec resolved this problem, and the rest of the code worked as expected!


## Second pass other learnings.

I wrote my own query func in golang. The clojure server was hitting multiple errors at ~21000 entries due to connections being kept alive.
Ends up that forgetting to close a request leaves it hanging :shock:. Which really breaks the server if you're trying to enqueue as many entries as possible.

```go
resp, err := http.Get("example.com")
defer resp.Body.Close() // NEVER forget this line.
```

After adding a defer Close. The clojure server started working as expected.
