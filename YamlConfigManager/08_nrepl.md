https://gist.github.com/thickey/fec7602d3801087a331a4507e137ed11

More interesting than expected. Needed to swap to the nrepl package as this was moved out of clojure.tools


➜  yaml-config-manager git:(main) ✗ clojure -X:dev -abc=1 -def=2
"hi"
"{-abc=1 -def=2}"


Seems like some of the problems I was hitting were due to the required namespaces directly calling run-jetty without any wrapper.

In yaml-config-manager.dev.user
```clojure
(ns yaml-config-manager.dev.user
  (:use [nrepl.cmdline])
  (:require [yaml-config-manager.server]))
```

In yaml-config-manager.server
```clojure
(ns yaml-config-manager.server
  (:use [ring.adapter.jetty])
  (:require [clojure.data.json]))

(use 'yaml-config-manager.server :reload-all) 
...
(run-jetty handler {:port 3000}) ; Was causing the server to start immediately on require being hit.
```

---

https://nrepl.org/nrepl/usage/server.html

https://ericnormand.me/guide/clojure-web-tutorial

---

It appears that a big reason my aliases have been failing is because my folder structure looked like this

yaml-config-manager
  resources
  src
    yaml_config_manager
      <code files>
      dev
        user.clj

And the yaml-config-manager was tasked with finding these, I never specified that yaml_config_manager is a sub-directory so it wouldn't find the code.

```clojure
 :aliases
  {:dev {:extra-paths ["src/yaml_config_manager/dev"]
         :extra-deps {nrepl/nrepl {:mvn/version "0.9.0"}}
         :exec-fn yaml-config-manager.dev.user/ss}
   }
```

Calling `clojure -A:dev` this way hit an error, buut found the user.clj file I was trying to compile. While putting the below was failing.


```clojure
 :aliases
  {:dev {:extra-paths ["dev"]
         :extra-deps {nrepl/nrepl {:mvn/version "0.9.0"}}
         :exec-fn yaml-config-manager.dev.user/ss}
   }
```

---

Something important to keep in mind is that if you want to changes made to the REPL to be reflected in the running code, you'll need to ensure that the function being ran is looked up at runtime.

```clojure
(defn handler [request]
  (do
    (reset! re request)
    {:status 200
   :headers {"Content-Type" "application/json"}
   :body "{'response': 'woot woot'"}))

(defn h [request]
  (handler request))

(defn start []
  (reset! server (run-jetty h {:port 3000 :join? false})))
```

Here whenever the jetty servelet receives a response the handler function is checked again allowing us to interact with the program without restarting the server.

Passing handler directly to run-jetty results in the body 'woot woot' always being returned regardless of what the handler var is currently associated to.

---

Somethign small to keep in mind is that even though this process gets out of the way, whenever deps.edn is updated the server process will still need to be restarted.
