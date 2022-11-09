# Paralleism

So we have this block of code which saves data to 2 separate databases. However its happening in synchronous manner. One thing that would be nice is to be able to run it a bit faster, to do this we're going to attempt to run it async using future.

```clj
(defn generate-sample-table-data-future [gen-definition system]
  (let [data (gen/eval-gens gen-definition)
                gen-id (second (first (data-gen-loader.domain.sample-table/save-data (:database system) (apply dissoc data [:id :primary-key]))))]
            (data-gen-loader.domain.sample-table/save-pk-table-data (:database-key-lookup system)
                                                                    (assoc (select-keys data [:town]) :id gen-id)))
  )

(defn generate-sample-table-data [qty system]
  (let [overrides {:name (gen/val-gen-string)
                   :town (gen/val-gen-rand-from-list ["Earth", "Moon", "Antarctica"])}
        gen-definition (gen/create data-gen-loader.domain.sample-table/definition overrides)
        ]
    ; TODO: Goal is to do this next part in parallel. No idea how to do that right now though, so will be doing it in sequence.
    ; https://www.youtube.com/watch?v=BzKjIk0vgzE -- claypoole talk
    ; Alternative strategy will be to use core.aysnc channels. Maybe do both to learn more?
    (doseq [i (range qty)]
      (generate-sample-table-data-future gen-definition system))))
```


```clj
(defn generate-sample-table-data-future [gen-definition system]
  (future (let [data (gen/eval-gens gen-definition)
                gen-id (second (first (data-gen-loader.domain.sample-table/save-data (:database system) (apply dissoc data [:id :primary-key]))))]
            (data-gen-loader.domain.sample-table/save-pk-table-data (:database-key-lookup system)
                                                                    (assoc (select-keys data [:town]) :id gen-id))))
  )

(defn generate-sample-table-data [qty system]
  (let [overrides {:name (gen/val-gen-string)
                   :town (gen/val-gen-rand-from-list ["Earth", "Moon", "Antarctica"])}
        gen-definition (gen/create data-gen-loader.domain.sample-table/definition overrides)
        ]
    ; TODO: Goal is to do this next part in parallel. No idea how to do that right now though, so will be doing it in sequence.
    ; https://www.youtube.com/watch?v=BzKjIk0vgzE -- claypoole talk
    ; Alternative strategy will be to use core.aysnc channels. Maybe do both to learn more?
    (doseq [i (range qty)]
      (generate-sample-table-data-future gen-definition system))))
```
Oops. For small numbers of inserts this works just fine, such as `         (generate-sample-table-data 10 system)`. But this caused an OutOfMemory exception and computer crash due to using all available threads. This doesn't seem optimal at all. (Around 515 items were added before we hit the crash on my 2015 Macbook)

Lets look into smarter alternatives.

---

First off refactoring the code to a format which would make alternating between pmap and map easy.

```clj
(defn generate-sample-table-data-future [gen-definition system]
  (let [data (gen/eval-gens gen-definition)
                gen-id (second (first (data-gen-loader.domain.sample-table/save-data (:database system) (apply dissoc data [:id :primary-key]))))]
            (data-gen-loader.domain.sample-table/save-pk-table-data (:database-key-lookup system)
                                                                    (assoc (select-keys data [:town]) :id gen-id))))


(defn generate-sample-table-data [qty system]
  (let [overrides {:name (gen/val-gen-string)
                   :town (gen/val-gen-rand-from-list ["Earth", "Moon", "Antarctica"])}
        gen-definition (gen/create data-gen-loader.domain.sample-table/definition overrides)
        ]
    ; TODO: Goal is to do this next part in parallel. No idea how to do that right now though, so will be doing it in sequence.
    ; https://www.youtube.com/watch?v=BzKjIk0vgzE -- claypoole talk
    ; Alternative strategy will be to use core.aysnc channels. Maybe do both to learn more?
    (doall (map #(% gen-definition system) (take qty (repeat generate-sample-table-data-future))))
    nil
    ))
```

(time (generate-sample-table-data 1000 system))
"Elapsed time: 2399.171265 msecs"

Let's try a naive map -> pmap swap with no changes to generate-sample-table-data-future.

Interesting. This is not at all what I expected.
(time (generate-sample-table-data 1000 system))
"Elapsed time: 12348.627217 msecs"

Seems like at 1,000 or 10, the pmap version takes 5x longer than map...

Is that because I need to add futures for pmap?

---

```clj
(defn generate-sample-table-data-future [gen-definition system]
  (future (let [data (gen/eval-gens gen-definition)
                gen-id (second (first (data-gen-loader.domain.sample-table/save-data (:database system) (apply dissoc data [:id :primary-key]))))]
            (data-gen-loader.domain.sample-table/save-pk-table-data (:database-key-lookup system)
                                                                    (assoc (select-keys data [:town]) :id gen-id)))))


(defn generate-sample-table-data [qty system]
  (let [overrides {:name (gen/val-gen-string)
                   :town (gen/val-gen-rand-from-list ["Earth", "Moon", "Antarctica"])}
        gen-definition (gen/create data-gen-loader.domain.sample-table/definition overrides)
        ]
    ; TODO: Goal is to do this next part in parallel. No idea how to do that right now though, so will be doing it in sequence.
    ; https://www.youtube.com/watch?v=BzKjIk0vgzE -- claypoole talk
    ; Alternative strategy will be to use core.aysnc channels. Maybe do both to learn more?
    (doall (pmap #(% gen-definition system) (take qty (repeat generate-sample-table-data-future))))
    nil
    ))
```

This is what I'll be running.

(time (generate-sample-table-data 10 system))
"Elapsed time: 2.066873 msecs"
=> nil
(time (generate-sample-table-data 10 system))
"Elapsed time: 0.551489 msecs"
=> nil
(time (generate-sample-table-data 1000 system))
"Elapsed time: 176.387513 msecs"
=> nil
(time (generate-sample-table-data 5000 system))
[919.709s][warning][os,thread] Failed to start thread - pthread_create failed (EAGAIN) for attributes: stacksize: 1024k, guardsize: 4k, detached.
[919.709s][warning][os,thread] Failed to start thread - pthread_create failed (EAGAIN) for attributes: stacksize: 1024k, guardsize: 4k, detached.

(crash again. lol)

Claypoole worked.

```clj

(defn generate-sample-table-data-future [gen-definition system]
  (try
    (let [data (gen/eval-gens gen-definition)
          gen-id (second (first (data-gen-loader.domain.sample-table/save-data (:database system) (apply dissoc data [:id :primary-key]))))]
      (data-gen-loader.domain.sample-table/save-pk-table-data (:database-key-lookup system)
                                                              (assoc (select-keys data [:town]) :id gen-id)))
    (catch SQLiteException e
      (println "Exception: " (ex-message e)))))


(defn generate-sample-table-data [qty system]
  (let [pool (cp/threadpool 50)
        overrides {:name (gen/val-gen-string)
                   :town (gen/val-gen-rand-from-list ["Earth", "Moon", "Antarctica"])}
        gen-definition (gen/create data-gen-loader.domain.sample-table/definition overrides)
        ]
    (doall (cp/pmap pool #(% gen-definition system) (take qty (repeat generate-sample-table-data-future))))
    nil
    ))
```

Some things to note. SQLite is not particularly great for parallelism, we had about 23 "The database file is locked" exceptions with a threadpool of 50 when inserting 5000 records.

Time to insert 5000 records was:
(time (generate-sample-table-data 5000 system))
 "Elapsed time: 13161.614915 msecs".

Compared to the map version it appears that parellism slows us down. From a gut level without much investigating, this makes a lot of sense. We're interacting with a file, sqlite, that has locks. Putting a ton of requests to make changes to this file increases the coordination overhead required by SQLite itself to get any changes committed. I'm going to be leaving the cp/pmap version of the code committed as moving to Cassandra for the main table could lead to noticeable insert time decreases.

map version
(time (generate-sample-table-data 1000 system))
"Elapsed time: 2399.171265 msecs"

cp/pmap version
(time (generate-sample-table-data 1000 system))
"Elapsed time: 3338.925978 msecs"

