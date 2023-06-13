# Reitit

Reading the documentation has helped me a lot. The Startrek application felt overwhelming at first, but I'm starting to piece together a mental map.


Documentation link (2023-06-07)
https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/introduction

## Basics:
Router: Responsible for matching URL's to something. Reitit has a lot of functionality that I don't expect to ever need (in particular dynamic routing). The router in most cases will be a mixed-router using some trie routers and some hash based routers. This allows it to be as fast as possible for pretty much all possible use cases. The routes themselves are what dictate handlers.

https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/ring/ring-router

Something to note is `:reitit.middleware/registry	Map of keyword => IntoMiddleware to replace keyword references into Middleware`. The router can be given middleware itself, typically done as a collection of data. (Compare this to Ring without any additional frameworks where you chain warppers directly).

Routes: 
Handle specifying the middlewares needed, and the handlers. Haven't quite gotten here yet.


Middleware:
https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/ring/data-driven-middleware

```txt
key	description
:name	Name of the middleware as a qualified keyword
:spec	clojure.spec definition for the route data, see route data validation (optional)
:wrap	The actual middleware function of handler & args => request => response
:compile	Middleware compilation function, see compiling middleware.
```

:middleware is merged to endpoints by the router.
```clj

```

## Naming routes

```clj
(-> (r/router
      ["http://localhost:8080/api/user/{id}" ::user-by-iad])
    (r/match-by-path "http://localhost:8080/api/user/123"))
```

`::user-by-iad` here represents a namespaced symbol reference and is placed into the route's :name field.

If you do `:user-by-iad` it places `{:name :user-by-iad}` as the name (no namespacing).

## Ring + Reitit

https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/ring/default-handler

Need to provide what to do in case no routes match.

Typically will be something like

`(ring/create-default-handler)`

## Static Files

External routes
A better way to serve files from conflicting paths, e.g. "/*", is to serve them from the default-handler. One can compose multiple default locations using reitit.ring/ring-handler. This way, they are only served if none of the actual routes have matched.

```clj
(ring/ring-handler
  (ring/router
    ["/ping" (constantly {:status 200, :body "pong"})])
  (ring/routes
    (ring/create-resource-handler {:path "/"})
    (ring/create-default-handler)))
```

# For future David

Swagger generation from :parameters and :responses.
https://cljdoc.org/d/metosin/reitit/0.7.0-alpha4/doc/ring/swagger-support


# For other future David

Interceptor pattern instead of middleware.
https://quanttype.net/posts/2018-08-03-why-interceptors.html

:enter and :leave which both interact on contexts.

Makes handling async and sync more straightforward.
