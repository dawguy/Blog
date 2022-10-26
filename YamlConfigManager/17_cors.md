# CORS
This is a pain in the butt as I don't quite understand what I need to do.

## Part 1
OPTIONS pre-flight request

Sends request without a BODY param. Needed to do (if (seqable? (:body request)) (continue-func) {}) otherwise it errored out every time.


## Part 2
Figuring out what params need to be set...

Ends up there are 2 fields.

1 which specifies which Origin (host) should be expected by the request. And another which specifies which headers are allowed to be sent to the API endpoint.


```clojure
(-> (handler response)
    (r/header "Content-Type" "application/json")
    (r/header "Access-Control-Allow-Origin" "http://localhost:4200")
    (r/header "Access-Control-Allow-Headers" "access-control-allow-origin content-type"))))
```

```clojure
(-> (handler response)
    (r/header "Content-Type" "application/json")
    (r/header "Access-Control-Allow-Origin" "http://localhost:3000") ; URL of the angular app causes the request to fail
    (r/header "Access-Control-Allow-Headers" "access-control-allow-origin content-type"))))
```

Note here:

I have "http://localhost:3000" as the angular app and "http://localhost:4200' as my clojure server.

Also worth noting. The access-control-allow-origin header from the client doesn't seem to do anything. The below works just as well.

```clojure
(-> (handler response)
    (r/header "Content-Type" "application/json")
    (r/header "Access-Control-Allow-Origin" "http://localhost:4200")
    (r/header "Access-Control-Allow-Headers" "content-type"))))
```
