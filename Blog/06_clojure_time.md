# Clojure

## Basis

https://github.com/seancorfield/usermanager-example

Basing off this as usual.

## Handlers, Ring, and Compojure

Once again I forgot something simple...

```clj
(fn [req] "Hello world!")
```

The above function is a handler.

```clj
(fn [handler]
    (fn [req]
        (handler (assoc req :from-wrap true))))
```

This takes in a handler and returns a handler.

This allows handlers to be chained as seen below.

```clj
(handler-3 (handler-2 (handler-1 req)))
```
Unsure why this took me 30 minutes to remember, but hopefully this time it clicked for good. (Granted its been 6+ months since I tried to write a web server in clojure. Definitely find scripting to be my favorite part of Clojure)

## Compojure

Still haven't really groked this library yet.

## JDBC

Something that tripped me up a bit, and I'd need to do some additional reading about is how the default result-set works.

https://clojuredocs.org/clojure.datafy/datafy

From my reading it seems like the two below blocks show how the data was set. 

`(keyword q (.getColumnLabel rsmeta i))` this seems like creates the structure that I had some issues parsing. It automatically namespaces the keyword for us, likely I could've found this before deciding to use the unnamespaced-result-set version of the functions.

```clj
(keyword "abc" "123")
=> :abc/123

(keyword "abc" "123" "456")
Execution error (ArityException) at blog-backend.main/eval10095 (form-init10704275919049529126.clj:1).
Wrong number of args (3) passed to: clojure.core/keyword
```


```clj
(defn as-maps
  "Given a `ResultSet` and options, return a `RowBuilder` / `ResultSetBuilder`
  that produces bare vectors of hash map rows.

  This is the default `:builder-fn` option."
  [^ResultSet rs opts]
  (let [rsmeta (.getMetaData rs)
        cols   (get-column-names rsmeta opts)]
    (->MapResultSetBuilder rs rsmeta cols)))

(defn as-unqualified-maps
  "Given a `ResultSet` and options, return a `RowBuilder` / `ResultSetBuilder`
  that produces bare vectors of hash map rows, with simple keys."
  [^ResultSet rs opts]
  (let [rsmeta (.getMetaData rs)
        cols   (get-unqualified-column-names rsmeta opts)]
    (->MapResultSetBuilder rs rsmeta cols)))
```

```clj
(defn get-column-names
  "Given `ResultSetMetaData`, return a vector of column names, each qualified by
  the table from which it came."
  [^ResultSetMetaData rsmeta _]
  (mapv (fn [^Integer i]
          (if-let [q (not-empty (get-table-name rsmeta i))]
            (keyword q (.getColumnLabel rsmeta i))
            (keyword (.getColumnLabel rsmeta i))))
        (range 1 (inc (if rsmeta (.getColumnCount rsmeta) 0)))))

(defn get-unqualified-column-names
  "Given `ResultSetMetaData`, return a vector of unqualified column names."
  [^ResultSetMetaData rsmeta _]
  (mapv (fn [^Integer i] (keyword (.getColumnLabel rsmeta i)))
        (range 1 (inc (if rsmeta (.getColumnCount rsmeta) 0)))))
```

```clj
[next.jdbc :as jdbc]
[next.jdbc.result-set :as rs]


(let lines (jdbc/execute! db ["select l.* from line l
                                         join content c
                                           on c.content_id = l.content_id
                                        where c.post_id = ?"
                                      post-id]
                               {:builder-fn rs/as-unqualified-maps })])
```

---

## Overall

Putting together the server required more patience than most things in Clojure. Felt like the plumbing was very hard to design in a REPL, but once it got to the stage where I was able to capture a request in an `def a-req (atom{})` it became very pleasant again.

One thing that I have been noticing while working through learning the library is that there certainly are some cases of not knowing what to expect in a field where types would've saved me some time. But maybe this would be something more apt to be solved by writing malli/spec for the functions.
