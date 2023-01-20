# CORS
This is a pain in the butt as I don't quite understand what I need to do.

## Part 1
OPTIONS pre-flight request

Sends request without a BODY param. Needed to do (if (seqable? (:body request)) (continue-func) {}) otherwise it errored out every time.

## Part 2

Dealt with this more at work and think that I pretty much have a grasp of what's happening and generally how this works. When the server is misconfigured and owned by somebody else its a pain as you'll need to talk with them to fix it, but for self hosted apps it should be pretty straight-forward to get working with minimal issues.

In the OPTIONS response from server we receive these two important headers. If you need fine grain control you can specify exact origins, also possible is using a wildcard to either allow everything or allow everything from a given host. Like "*.yamlConfigManager.com" or "*". The Access-Control-Allow-Headers is sent back and specifies which headers the server was set up to accept. If a server has a stricter set of headers in this field than what is being provided from the client then you'll need
to remove headers client side or ask the server owner to be less stringent.
```
Access-Control-Allow-Headers: access-control-allow-origin, content-type
Access-Control-Allow-Origin: http://localhost:4200
```

From client side this headers are all auto-genereated for the OPTIONS request based on what is put into the main POST request. You can compare the Origin and Access-Control-Request-Headers with what the server is expecting in order to debug any problems that arrise.

````
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: POST
Host: localhost:3000
Origin: http://localhost:4200
Referer: http://localhost:4200/
```


## Part 3
Ends up there are 2 fields that need updating server side for the OPTIONS preflight request headers to work as expected.

1 which specifies which Origin (host) should be expected by the request and another which specifies which headers are allowed to be sent to the API endpoint.

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

