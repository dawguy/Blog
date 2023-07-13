# Reitit Has Multiple Ways To Get Parameters In

https://github.com/metosin/reitit/blob/master/doc/coercion/malli_coercion.md

This is how I'm calling everything.

```clj
(require 'reitit.coercion.malli :as coe)
(require 'reitit.coercion :as c)
(require 'reitit.ring :as ring)

(def app
  (ring/ring-handler
    (ring/router
      [(#'public-routes)]
      {:data {:middleware [reitit.ring.coercion/coerce-exceptions-middleware
                           reitit.ring.coercion/coerce-request-middleware
                           reitit.ring.coercion/coerce-response-middleware]
              :compile    c/compile-request-coercers}})))

(app {:request-method :get
      :uri            "/product/1"})
```

The coercion guide is quite good, but I wanted to lay out my findings.
```clj
(defn public-routes []
[""
  ["/product"
     ["/:id" {:get {:handler product/get-product
                    :coercion coe/coercion
                    :parameters {:path [:map
                                        [:id pos-int?]]}
                    }}]] ]
```

# What differnet route combos look like

```clj`
    ["/product"
     ["/:id" {:get {:handler product/get-product}}]]
```

Get product with path params {:id "1"}. Parameters null. Is id a string = true

```clj
    ["/product"
     ["/:id" {:get {:handler product/get-product
                    :parameters {:path [:map
                                        [:id pos-int?]]}
                    }}]]
```

Get product with path params {:id "1"}. Parameters null. Is id a string = true

```clj
    ["/product"
     ["/:id" {:get {:handler product/get-product
                    :coercion coe/coercion
                    }}]]
```

Get product with path params {:id "1"}. Parameters null. Is id a string = true


```clj
    ["/product"
     ["/:id" {:get {:handler product/get-product
                    :coercion coe/coercion
                    :parameters {:path [:map
                                        [:id pos-int?]]}
                    }}]]
```

Get product with path params {:id "1"}. Parameters {:path {:id 1}}. Is id a string = true

# Summary

Coercion works, but needs to be done to copmletion. If you attempt to do it in a partial way it will not succeed.

# After A Bit Of Tinkering

Got it pretty much all working. Do have some issues with the error messages occasionally being hard to diagnose. For example when there is a typo in the schema.

[:map :id pos-int?] -- Wrong! Gives very unhelpful :ref exception.
[:map [:id pos-int?]] -- Correct. Took an hour to debug this one though as I was looking at the router.
