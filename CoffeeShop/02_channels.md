# Channels

https://www.reddit.com/r/golang/comments/scioc3/having_a_hard_time_understanding_goroutines_and/hu787xr/


So the three most basic concurrency patterns are the pipeline, fan-out, and fan-in.

Pipelines are where a channel is basically the holder for a value at the boundary between steps in a process. If each step in the process takes its input from a channel and hands the output to a channel, you get several nice effects. First, each step can run in its own goroutine and be in parallel. Second it’s a nice decoupling making each step easily testable.

Fan-out is a pattern where jobs arrive in a channel and many goroutines can pick it up. This allows many workers to do the same task. This is how web servers work.

As you might guess, fan-in is the opposite. Here, a channel can be the destination for many different jobs. This takes parallel data flows and makes them flow in series. An example you’d use this for is when you’re working with a resource that only allows single-threaded access like a GUI dispatch thread or a SQLite database. So you would have many goroutines making change requests for the database and they would all put them into the same channel. On the other side of the channel a dispatcher goroutine would take them off one by one and make the changes to the database.

Once you have those three building blocks, you can mix and match. Like you could listen to a socket in one goroutine, and the listener would put all the requests in a channel. Then a hundred request processor goroutines could take the requests first-come-first-served and do some work on them, and put them in a results channel, where a goroutine can take the results one at a time and write them to disk.

## Other Tidbits

The goblocks don't block. Meaning you can call them to start processing channels without having to worry about how to return the channels.

```clj
(
  (prn "A")
  (a/go (prn "B")
     (let [a (a/chan)
           b (a/<! a)]
        (prn "b done")))
  (a/go (prn "C")
       (let [a (a/chan)
             b (a/<! a)]
          (prn "c done")))
)

# Prints
# "A"
# "B"
# "C"
```

My initial take was differnet and I was planning on doing something like below

```clj
(defn create-grinder [name]
  (let [in-c (a/chan)
        out-c (a/chan)
        f (fn [] "Starts the grinder"
          (a/go (loop [i 0]
                (let [[v c] (a/alts! [in-c (a/timeout timeout-length)])]
                  (if (= c in-c)
                    (do
                      (my-prn i (str "Grinder: " name " handled " v))
                      (. Thread sleep grind-time)
                      (a/>! out-c (assoc v :state "ground"))
                      (recur (inc i)))
                    (do
                      (println (str "Closing grinder: " name))
                      (a/close! in-c)
                      (a/close! out-c))
                    )))))]
    [f in-c out-c]))
```

And this to start them all
```clj
    (doseq [g (->v grinders 0)] (g))
```

## Other Learnings

Alts can be used for both <! and >!
```clj
(let [placed {:state "order-placed" :c c}
      [v port] (a/alts! (mapv #(vector % placed) (->v grinders 1)))]
   ; <alts code here>    
)
```

From documentation

If ports has a channel it will do a <!
If ports has a vector [port value] it will do a >! of the value to the port

https://clojuredocs.org/clojure.core.async/alts!

## Printing results 

The naive way doesn't quite work as you'd expect. Since we're creating out-c immediately, but values are filled in async we end up hitting the nil? conditional immediately.

```clj
(let [out-c (run-sim 50)]
  (a/go-loop [coffees []]
    (let [v (a/poll! out-c)]
      (if (nil? v)
        (do
          (prn coffees)
          coffees)
        (recur (conj coffees v))))))
```

This works as you'd expect.
```clj
(let [out-c (run-sim 50)]
  (a/go-loop [coffees []]
    (let [[v port] (a/alts! [out-c (a/timeout timeout-length)])]
      (if (nil? v)
        (do
          (prn coffees)
          coffees)
        (recur (conj coffees v))))))
```

---

## :default and :priority

Default seems quite useful. Seems like a way to take an alt! and return immediately if blocked.

http://bytopia.org/2016/09/14/implementing-leaky-channels/

```clj
(a/alt! c ([v] (if v
            (recur (dec i)) ; Value dropped, go drop another.
            true ; nil returned instantly = channel closed
            ))
   :default false ; take blocked = channel empty, continue.
   :priority true)
```
