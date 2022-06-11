# Opening In Chrome Via Shell 
Up next is getting google chrome to open


## Linux
```sh
xdg-open http://example.com & xdg-open http://example.com/2
```

## Mac
```sh
open -a http://example.com http://example.com/2
```

---

https://pwdd.github.io/post/running-shell-commands-with-clojure

Basic idea here is to use what's already been written to generate the string then execute it.

That was incredibly easy. 

```clojure
(defn open-in-browser [s] (shell/sh "xdg-open" s))
```

All that really needs to be done now is decide if/how I want to pull in an actual list of microservices.


This also looks tempting instead of using the shell commands open and xdg-open.
https://clojuredocs.org/clojure.java.browse/*open-url-script*

* Ends up that this is pretty much a pre-implemented version of exactly what I was looking into. Almost to the t.

Did learn some pretty neat stuff by reading the existing implementation.


```clojure
(defn browse-url
  "Open url in a browser"
  {:added "1.2"}
  [url]
  (let [script @*open-url-script*
        script (if (= :uninitialized script)
                 (reset! *open-url-script* (open-url-script-val))
                 script)]
    (or (when script (sh/sh script (str url)) true)
        (open-url-in-browser url)
        (open-url-in-swing url))))

```

(From https://clojuredocs.org/clojure.java.browse/*open-url-script*#example-54b13a77e4b0e2ac61831ca4)
```clojure
;; Sets the script used when calling browse-url
;;
;; It needs to be an atom because internally the browse-url function
;; dereferences *open-url-script*
(binding [clojure.java.browse/*open-url-script* (atom "/usr/bin/curl")]
  (clojure.java.browse/browse-url "http://google.com"))
```

Essentially the binding @*open-url-script* allows us to redefine the functions used without having to touch the source code. Really useful thing to remember since it allows us to completely avoid messy param lists of functions when there is a sensible default available.


---

Another interesting thing is that babashka seems to gracefully terminate while clj needed some assitance.


`bb src/microservice_navigator.clj` terminated as soon as program was done.

`clj src/microservice_navigator.clj -X microservice-navigator/run` required (shutdown-agents) to be called or ctrl+c to be received. Most likely because of the `sh` program used to launch the browser uses the agent pool (I think this is like a thread pool that backs agent system https://clojure.org/reference/agents) based on https://clojureverse.org/t/why-doesnt-my-program-exit/3754/4


