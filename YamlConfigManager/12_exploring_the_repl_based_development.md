# Exporing REPL based development

Currently I have a postman collection which sends in requests, and wanted to use the repl to help me develop faster.

Often what I've been doing so far is something that looks like this. Define the variables with identical names in a comment block. And setting them to whatever I wanted for development purposes.

```clojure
(comment "Helper values for development of file reading features"
  (def body {"fromFile" {"env" "development", "fileName" "serviceA.yml", "serviceName" "serviceA"},
             "toFile" {"env" "staging", "fileName" "serviceA.yml", "serviceName" "serviceA"}})
  (def target-dir ".")
  (def target-dir "./sample_project_configs")
  (def m {:TMP "val"})
)
```

What I'm planning to try right now is creating atoms

```clojure
(def from-file-infos (atom {}))
(def to-file-infos (atom {}))
```

And setting them up during the first call from Postman.

```clojure
(defn migrate-properties-env [body]
  (let [body-parsed (assoc-file-paths body)
        from-file-infos (vals (get-in @app-db [:environments (:env (first body-parsed))]))
        to-file-infos (vals (get-in @app-db [:environments (:env (second body-parsed))]))]
    (prn body-parsed)
    (prn from-file-infos)
    (prn to-file-infos)
    (reset! yaml-config-manager.manager/from-file-infos from-file-infos)
    (reset! yaml-config-manager.manager/to-file-infos to-file-infos)
    (doseq [[from-file-info to-file-info] (group-env-file-infos @from-file-infos @to-file-infos)]
      (config/assoc-selected-props to-file-info (config/get-changeset from-file-info to-file-info)))
    ))
```
The idea is that I'll be able to send the request once and not have to worry about getting something exactly correct setup.

```txt
({:name "serviceC.yml", :service "serviceC", :env "staging", :full-path "./sample_project_configs/staging/serviceC/serviceC.yml", :f #object[java.io.File 0x17225f4a "./sample_project_configs/staging/serviceC/serviceC.yml"], :exists true, :yaml #ordered/map ([:serviceC #ordered/map ([:name "Service Charlie"] [:deploymentType "NPE"])] [:featureA #ordered/map ([:enabled true])] [:featureB #ordered/map ([:url "featureBURL"] [:enabled true])] [:featureCFlag true] [:featureD #ordered/map ([:url "featureDURL"] [:enabled true])] [:featureRemoved #ordered/map ([:enabled false])])} {:name "serviceB.yml", :service "serviceB", :env "staging", :full-path "./sample_project_configs/staging/serviceB/serviceB.yml", :f #object[java.io.File 0x10bcbc75 "./sample_project_configs/staging/serviceB/serviceB.yml"], :exists true, :yaml #ordered/map ([:database #ordered/map ([:username "databaseUser"] [:password "databasePassword"] [:connection #ordered/map ([:url "databaseIP,databaseIP2"] [:port 4567])])] [:serviceB #ordered/map ([:name "Service Beta"] [:special "Only B Has This Property"] [:deploymentType "NPE"])] [:featureA #ordered/map ([:enabled true])] [:featureB #ordered/map ([:url "featureBURL"] [:enabled true])] [:featureCFlag true] [:featureD #ordered/map ([:url "featureDURL"] [:enabled true])] [:featureRemoved #ordered/map ([:enabled false])])} {:name "serviceA.yml", :service "serviceA", :env "staging", :full-path "./sample_project_configs/staging/serviceA/serviceA.yml", :f #object[java.io.File 0x58a69acf "./sample_project_configs/staging/serviceA/serviceA.yml"], :exists true, :yaml #ordered/map ([:database #ordered/map ([:username "databaseUser"] [:password "databasePassword"] [:connection #ordered/map ([:url "databaseIP,databaseIP2"] [:port 4567])])] [:serviceA #ordered/map ([:name "Service Alpha"] [:deploymentType "NPE"])] [:featureA #ordered/map ([:enabled true])] [:featureB #ordered/map ([:url "featureBURL"] [:enabled true])] [:featureCFlag true] [:featureD #ordered/map ([:url "featureDURL"] [:enabled true])] [:featureRemoved #ordered/map ([:enabled false])])})
```
Since this block includes several things that aren't mapped by a copy+paste (File references and #ordered/map)

---

Below I'm planning on putting my findings about whether or not this is worth doing. I'm quite certain that there are tools to do this, so I'm betting it will be.

```clojure
(comment "Redefine helper atoms into def'd vars"
  (def from-file-infos (deref from-file-infos))
  (def to-file-infos (deref to-file-infos))
)
```

This is the helper I added to make life even easier. It translates from an atom to a def'd variable. Then can be used exactly as my commented variables can.

100% will be doing this again in the future as it makes life way simpler.

---

One thing I did notice near the end was that by redefining the atoms to normal variables with the exact same nameI caused the function to stop working. In the future when I want to do something similar it probably will make sense to prefix the atom with a-, that way this problem won't occur.
