# Ring

https://www.baeldung.com/clojure-ring

Many tutorials but something I had a hard time wrapping my head around is that the middleware is wrapping function call chains, not the results of the function calls themselves.

```clojure
(-> handler
    wrap-json-body
    wrap-json-response)

(wrap-json-response (wrap-json-body (handler)))
```

## Orderings

Let's check how ordering matters

Sample code where handler returns a 200 response.
```clojure
(defn attach-b-val1 [handler]
  (fn [request]
    (assoc handler :b "1")))

(defn attach-b-val2 [handler]
  (fn [request]
    (assoc handler :b "2")))

(defn app [request]
  ((-> handler
       wrap-json-body
       wrap-json-response
       attach-b-val1
       attach-b-val2) request)
  )
```

My expectation here is that the handler attach-b-val2 will be executed first associng :b 2, then attach-b-val1 will be exectued overwriting that value with :b 1.

Ends up I had a misunderstanding somewhere as this didn't work. Ah. I needed to assoc to the response, not the request.

Full working snippet below. The middleware has some leeway in how it wants to be handled as they are all just code. In general request edits will be made First In Last Out while response edits will be made First In First Out based on the ordering of the -> macro threading.
```clojure
(defn handler [request]
    {:status  200
     :headers {"Content-Type" "application/json"}
     :body    {:body "woot woot!" :a (:server-name request)}})

(defn attach-b-val-a [handler]
  (fn [request]
      (assoc-in (handler request) [:body :b] "1")))

(defn attach-b-val-b [handler]
  (fn [request]
      (assoc-in (handler request) [:body :b] "2")))

(defn app [request]
  ((-> handler
       attach-b-val-a
       attach-b-val-b
       wrap-json-body
       wrap-json-response
       ) request))

(defn h [request]
  (app request))

(defn start []
  (reset! server (run-jetty h {:port 3000 :join? false})))
```

Returns :b 2.
