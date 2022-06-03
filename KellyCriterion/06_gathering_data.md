Started doing a bit of manual data writing down trying to get S&P 500 data into one of my probability maps, then figured I've been wanting to try babashka and reading from CSVs for a while so why not do that here.

I wasn't sure if babashka was going to be able to deal with dependencies, but it handled it like a charm. And was blazingly fast to boot!

`clj src/core.clj` takes a noticeable amount of time to run. (Feels like 2 seconds)

`bb src/core.clj` feels like its done running before my finger comes off the enter key. Absolutely amazing.



Code for reading a csv file in the format year,percent change

```clojure
(ns core
   (:require [clojure.data.csv :as csv]
             [clojure.java.io :as io]))

 (defn parse-file [file-name]
   (->> (with-open [reader (io/reader file-name)] (doall (csv/read-csv reader)))
      (map (fn [a] {:year (first a) :payout-original (+ 100 (Double/parseDouble (second a)))}))
      (map #(assoc % :payout (Math/round ^double (:payout-original %))))
      (mapv identity)))

 (spit "data.txt" (parse-file "data.csv"))
```

I've previously tried manually parsing CSV data by slurping into a string then parsing the string. But this library made it incredibly easy and gave the file as a lazy-seq.

Only gotcha with this approach is that if you don't consume the lazy seq as part of a doall the io/reader will close. But this was pretty well documented on the dependency's page.

---

Overall spent a bit longer doing it this way, but ended up really happy that I did and kinda excited to see where I could use the data next.

---

One extra thing to note. The data.txt was in the format ` [{:year "2021", :payout-original 128.71, :payout 129} {:year "2020", :payout-original 118.4, :payout 118}]` . I just copy pasted this straight into the kelly-criterion-client as its only 5172 characters long and way simpler than trying to load this in as external data.
