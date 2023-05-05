# Malli

Chose Malli as it seems like its easier to use and better maintained than spec or schema.

The company who uses it is Finnish and seems to be very well respected.

Example
```clj
(def Product
  [:map
   [:id pos-int?]
   [:name :string]
   [:v [:enum {:title "Color"} "red" "black"]]
   [:cost [:double {:min 0}]]
   [:width [:double {:min 0}]]
   [:height [:double {:min 0}]]
   [:shape [:enum {:error/message "Should be square or circle"} "square" "circle"]]])

(comment "Malli helpers"
  (mg/generate Product)
  (m/schema Product)
  (m/ast Product)
,)
```


https://www.metosin.fi/blog/high-performance-schemas-in-clojurescript-with-malli-1-2/

```clj
schema
;[:map
; [:name string?]
; [:eatability int?]
; [:data [:maybe [:map
;                 [:taste [:vector keyword?]]
;                 [:latin {:optional true} string?]]]]]
```

Example app:

https://github.com/aarkerio/ZentaurLMS.git

Also an example app in the malli repo itself.

---
Gen docs: Allows generated data to be given structure.

https://github.com/metosin/malli#value-generation

```clj
;; :gen/min & :gen/max for numbers and collections
(mg/generate
  [:vector {:gen/min 4, :gen/max 4} :int]
  {:seed 1})
; => [-8522515 -1433 -1 1]


;; :gen/schema
(mg/generate
  [:any {:gen/schema [:int {:min 10, :max 20}]}]
  {:seed 10})
; => 19
```

## Inferring Schemas

https://github.com/metosin/malli#inferring-schemas

```clj
(require '[malli.provider :as mp])

(def samples
  [{:id "Lillan"
    :tags #{:artesan :coffee :hotel}
    :address {:street "Ahlmanintie 29"
              :city "Tampere"
              :zip 33100
              :lonlat [61.4858322, 23.7854658]}}
   {:id "Huber",
    :description "Beefy place"
    :tags #{:beef :wine :beer}
    :address {:street "Aleksis Kiven katu 13"
              :city "Tampere"
              :zip 33200
              :lonlat [61.4963599 23.7604916]}}])

(mp/provide samples)
;[:map
; [:id :string]
; [:tags [:set :keyword]]
; [:address
;  [:map
;   [:street :string]
;   [:city :string]
;   [:zip :int]
;   [:lonlat [:vector :double]]]]
; [:description {:optional true} :string]]
```
