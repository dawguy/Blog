# Requests

Ends up being quite straight-forward with the clj-http library. They are processed serially where the following calls wait for the results. Async are also possible results wait for 

```clojure
(defn say-hi [status url] (prn (str "Hello! You got a " status " response from " url)))

(say-hi (:status (client/head "http://example.com")) "http://example.com")
```

Does exactly what you expect. "Hello! You got a 200 repsonse from http://example.com"

Note if the URL doesn't exist or you're on an airplane/can't connect you instead hit an Execution error (UnkownHostException)

```clojure
; It appears that in order to send this it needs to be turned into a json string.
(def body-vars {
           "env" "development",
           "fileName" "serviceA.yml",
           "serviceName" "serviceA",
           "properties" { "featureCFlag" true,
                          "featureD.url" "updatedURLForSpringProperties"
                        }
           })

(def body-vars-str "{
                \"env\": \"development\",
                \"fileName\": \"serviceA.yml\",
                \"serviceName\": \"serviceA\",
                \"properties\": { \"featureCFlag\": true,
                              \"featureD.url\": \"updatedURLForSpringProperties\"
                              }
                }")

(def body-vars-jsonified (json/generate-string body-vars))

; Fails because we can't send a map
(client/post "http://localhost:3000/apply-properties-file"
             {
              :body body-vars
              :headers {}
              :content-type :json
              :accept :json
              })

; Fails because the `:` characters aren't being included
(client/post "http://localhost:3000/apply-properties-file"
             {
              :body (str body-vars)
              :headers {}
              :content-type :json
              :accept :json
              })

; Succeeds with hard-coded json string
(client/post "http://localhost:3000/apply-properties-file"
             {
              :body body-vars-str
              :headers {}
              :content-type :json
              :accept :json
              })

; Succeeds as we successfully JSON encoded the map
(client/post "http://localhost:3000/apply-properties-file"
             {
              :body body-vars-jsonified
              :headers {}
              :content-type :json
              :accept :json
              })
```

Above were some attempts to send a post request. Hit some problems since I didn't intitially think to send JSON and wanted to just send a clojure map. The easiest solution going forwards will be to JSONify any maps with something like chesire.
