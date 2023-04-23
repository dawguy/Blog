# Dockerizing Clojure

https://circleci.com/blog/package-a-clojure-web-application-using-docker/

Can't forget about chatGPT. Comes in clutch for answering questions and providing examples.

## Fixing Up Clojure

```clj
lein uberjar
```

The :gen-class was required. From https://clojureverse.org/t/gen-class-not-only-the-purpose-but-the-why/5178/3 it seems like all Java programs need a main method, and gen-class is how Clojure generates Java bytecode. "When compiling, generates compiled bytecode for a class with the given package-qualified :name (which, as all names in these parameters, can be a string or symbol), and writes the .class file to the *compile-path* directory."

So if you want to run a Clojure program with java -jar we'll always need to generate class for the main function. Generate class isn't needed when running with `clj -m:main`.

```zsh
➜  blog-backend git:(main) ✗ java -jar target/blog-backend-0.1.0-SNAPSHOT-standalone.jar
Error: Could not find or load main class blog_backend.main
Caused by: java.lang.ClassNotFoundException: blog_backend.main
```

```clj
(ns blog-backend.main
  (:require [com.stuartsierra.component :as component]
            [blog-backend.db :as db]
            [blog-backend.server :as server]
            )
  (:gen-class))
```

```zsh
➜  blog-backend git:(main) ✗ java -jar target/blog-backend-0.1.0-SNAPSHOT-standalone.jar
2023-04-20 15:03:35.986:INFO::main: Logging initialized @344ms to org.eclipse.jetty.util.log.StdErrLog
#blog_backend.db.Database{:db-spec {:dbtype "sqlite", :dbname "blog_db"}, :datasource #object[next.jdbc.connection$url_PLUS_etc$reify__1820 0x46188a89 "jdbc:sqlite:blog_db"]}
"handler setup"
"middleware-stack setup"
2023-04-20 15:03:36.753:INFO:oejs.Server:main: jetty-9.4.48.v20220622; built: 2022-06-21T20:42:25.880Z; git: 6b67c5719d1f4371b33655ff2d047d24e171e49a; jvm 17.0.6+0
2023-04-20 15:03:36.775:INFO:oejs.AbstractConnector:main: Started ServerConnector@55746340{HTTP/1.1, (http/1.1)}{0.0.0.0:8888}
2023-04-20 15:03:36.776:INFO:oejs.Server:main: Started @1134ms
"adding app component"
"my-middleware setup"
"my-middleware req"
{:ssl-client-cert nil, :protocol "HTTP/1.1", :cookies {}, :remote-addr "127.0.0.1", :params {}, :flash nil, :route-params {}, :headers {"accept" "*/*", "user-agent" "curl/7.86.0", "host" "localhost:8888"}, :server-port 8888, :content-length nil, :form-params {}, :compojure/route [:get "/post/recent"], :session/key nil, :query-params {}, :content-type nil, :character-encoding nil, :uri "/post/recent", :server-name "localhost", :query-string nil, :body #object[org.eclipse.jetty.server.HttpInputOverHTTP 0x63b54b9c "HttpInputOverHTTP@63b54b9c[c=0,q=0,[0]=null,s=STREAM]"], :multipart-params {}, :scheme :http, :request-method :get, :session {}}
"add-app-component req"
```

## The Dockerfile

https://www.baeldung.com/ops/docker/expose-vs-publish

Expose vs publish. Next up is how do we expose to public? (Most likely via reverse proxy)

https://circleci.com/blog/package-a-clojure-web-application-using-docker/
