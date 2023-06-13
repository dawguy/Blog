# Debugging

Was seeing this error. Seems like having the body as a map wasn't handled by the below set of middleware. Not quite sure why, but will spend a bit more time debugging this.

java.lang.IllegalArgumentException: No implementation of method: :write-body-to-stream of protocol: #'ring.core.protocols/StreamableResponseBody found for class: clojure.lang.PersistentArrayMap


```clj
:middleware [
    my-logging-middleware
    cors/cors-middleware
    headers/headers-middleware
    muuntaja/format-middleware
    coercion/coerce-request-middleware
    coercion/coerce-response-middleware ]

(defn public-routes []
  ["/test" {:get {:handler (fn [request]
                             (log/infof "Request hit!")
                             ;{:status 200 :body {} :headers {}} ; Errors
                             {:status 200 :body "Weo" :headers {}})
                 :parameters {}
                 :responses {}}}])
```

https://stackoverflow.com/questions/60572641/how-do-you-set-up-middleware-in-clojure-using-reitit-to-enable-coercion-of-body

https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/ring/default-middleware

Still not quite sure what's going wrong. / Why the muuntaja format-middleware does nothing.

```txt
[[INFO ][p.server.core  ] - Body: HttpInputOverHTTP@55352ab2[c=0,q=0,[0]=null,s=STREAM]
[[INFO ][p.server.core  ] - Body: HttpInputOverHTTP@55352ab2[c=0,q=0,[0]=null,s=STREAM]
[[INFO ][p.server.core  ] - Body: HttpInputOverHTTP@55352ab2[c=0,q=0,[0]=null,s=STREAM]
[[WARN ][j.s.HttpChannel] - /test
```

Ends up following the documentation go it working.

https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/ring/content-negotiation

This :data field was required for explicitly setting muutaja

```clj
{:data {:muuntaja m/instance
              :coercion rcs/coercion
              :middleware [muuntaja/format-middleware
                           rrc/coerce-exceptions-middleware
                           rrc/coerce-request-middleware
                           rrc/coerce-response-middleware]}}
```

Don't quite understand it as it uses the :compile option that I haven't dug into. Bu it seems like muuntaja requires something which states how to do the formatting.

https://www.metosin.fi/blog/muuntaja/

(Guide to muuntaja)

Seems like muutaja is built to convert from a <-> b. So it does kinda make sense that you wouldn't want this to be auto-assumed without being explicit as a design decision.

## Middleware can accept data that will be passed to context 

(and hence could be applied to all handlers) 

Note: The log messages are what show up when I try to start the server, these don't appear immediately.

```clj
(defn three-part-handler-mw [handler data-a data-b]
  (prn "3 part handler")
  (prn handler)
  (prn data-a)
  (prn data-b)
  (fn [request]
    (handler request)))

(defn public-routes []
  [["/test" {:post {:middleware [three-part-handler-mw {:a 1 :b 2} {:c 3 :d 4}]
                    :handler    auth/login}}]
   ]
  )
```

```txt
Execution error (ExceptionInfo) at reitit.exception/exception (exception.cljc:19).
Wrong number of args (1) passed to: planogrammed-groceries-backend.server.routes/three-part-handler-mw
```

But it works when wrapped like this. Be careful since it makes it pretty easy to accidentally double wrap something and have no clue why its breaking/erroring.

```clj
(defn three-part-handler-mw [handler data-a data-b]
  (prn "3 part handler")
  (prn handler)
  (prn data-a)
  (prn data-b)
  (fn [request]
    (handler request)))

(defn public-routes []
  [["/test" {:post {:middleware [[three-part-handler-mw {:a 1 :b 2} {:c 3 :d 4}]]
                    :handler    auth/login}}]
   ]
  )
```

```txt
"3 part handler"
#object[planogrammed_groceries_backend.auth.interface$login 0x5b1027c8 "planogrammed_groceries_backend.auth.interface$login@5b1027c8"]
{:a 1, :b 2}
{:c 3, :d 4}
```

In practice this bit me when I tried to do below.

```clj
:middleware [[basic-authentication-middleware post-basic-authentication-middleware] anti-forgery/wrap-anti-forgery]
```

Fix was

```clj
:middleware (conj [basic-authentication-middleware post-basic-authentication-middleware] anti-forgery/wrap-anti-forgery)
```

## Requests Not Hitting MW

Ends up that it was a request side issue. Needed to add Accept: `application/json` and the conversions worked as expected.


## Body Not Parsing

My logic seems to leave :body as `:body #object[org.eclipse.jetty.server.HttpInputOverHTTP`. But there is `:body-params` with the expected values. 

## Expiring The JWT

Had to add a :exp field to the JWT manually. Wasn't too bad at all to get working though in a basic capacity.

## Private/Public

The strategy from the (startrek)[https://github.com/dharrigan/startrek] project of splitting routes into public and private was quite pleasant to use.

## Exceptions & Encodings

Hitting the same issue :muuntaja fixed, but this time for exceptions.

{:status 401 :body "Test"} in the exception handler works. {:status 401 :body {:message "Test"}} does not :'(

Brain blast. I'd bet that the root cause is that Clojure/Reitit isn't magic. The hanlder chain is likely breaking early due to the exception, and therefore skipping the handlers (possibly).

Note: Brain blast did not work.

```clj
; Failed call looks like below.
[[INFO ][p.s.m.cors     ] - Response pre-cors is {:status 401, :body {:message "Failed to unsign token"}, :headers {"Cache-Control" "no-cache, no-store, max-age=0, must-revalidate", "Expires" "0", "Pragma" "no-cache", "X-Content-Type-Options" "nosniff", "X-Frame-Options" "DENY", "X-XSS-Protection" "1; mode=block", "Content-Type" "application/json", "Accept" "application/json"}}
[[INFO ][p.s.m.cors     ] - Attaching cors headers to response. {:status 401, :body {:message "Failed to unsign token"}, :headers {"Cache-Control" "no-cache, no-store, max-age=0, must-revalidate", "Expires" "0", "Pragma" "no-cache", "X-Content-Type-Options" "nosniff", "X-Frame-Options" "DENY", "X-XSS-Protection" "1; mode=block", "Content-Type" "application/json", "Accept" "application/json"}}

; Successful call looks like below.
[[INFO ][p.s.m.cors     ] - Response pre-cors is {:status 200, :headers {"Cache-Control" "no-cache, no-store, max-age=0, must-revalidate", "Expires" "0", "Pragma" "no-cache", "X-Content-Type-Options" "nosniff", "X-Frame-Options" "DENY", "X-XSS-Protection" "1; mode=block", "Content-Type" "application/json; charset=utf-8"}, :body #object[java.io.ByteArrayInputStream 0x41958939 "java.io.ByteArrayInputStream@41958939"]}
[[INFO ][p.s.m.cors     ] - Attaching cors headers to response. {:status 200, :headers {"Cache-Control" "no-cache, no-store, max-age=0, must-revalidate", "Expires" "0", "Pragma" "no-cache", "X-Content-Type-Options" "nosniff", "X-Frame-Options" "DENY", "X-XSS-Protection" "1; mode=block", "Content-Type" "application/json; charset=utf-8"}, :body #object[java.io.ByteArrayInputStream 0x41958939 "java.io.ByteArrayInputStream@41958939"]}
```

Gonna ask for help on slack for this one. Since I'm lost.

```clj
{:status http-status :body (m/encode "application/json" body)}
```

