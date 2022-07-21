# Intro To YAML

clj-yaml : https://github.com/clj-commons/clj-yaml
flatland-ordered-map : https://github.com/flatland/ordered

These two dependencies pretty much do everything we could want in terms of interacting with yaml files.

Using ordered maps will be very important for keeping commits based on the existing code readable


Unfortunately this means that we'll probably need to be very specific. Though we might be safe as these libraries seem smart enough to assoc-in into the ordered/map when interacting with existing things. The challenge is definitely going to be how to deal with creating clean looking commits automatically when somebody does something like accidentally removing a key.


```clojure
(def a (assoc-in (yaml/parse-string (slurp "sample_yaml/a.yaml")) [:top :properties :secondList] ["AAA" "BBB" "CCC"]))

=>
#ordered/map([:top
              #ordered/map([:properties
                            #ordered/map([:list "a,b,c,d,e"]
                                         [:abc "hello"]
                                         [:end "world"]
                                         [:secondList ["AAA" "BBB" "CCC"]])])]
             [:middle
              #ordered/map([:zzz #ordered/map([:watermelon "isWatermelon"] [:apple "isApple"])]
                           [:yo #ordered/map([:patience "startsWithLetterP"] [:apple "startsWithLetterA"])])]
             [:bottom "test"])


(assoc-in a [:middle :yo :properties :secondList] ["AAA" "BBB" "CCC"])

=>
#ordered/map([:top
              #ordered/map([:properties
                            #ordered/map([:list "a,b,c,d,e"]
                                         [:abc "hello"]
                                         [:end "world"]
                                         [:secondList ["AAA" "BBB" "CCC"]])])]
             [:middle
              #ordered/map([:zzz #ordered/map([:watermelon "isWatermelon"] [:apple "isApple"])]
                           [:yo
                            #ordered/map([:patience "startsWithLetterP"]
                                         [:apple "startsWithLetterA"]
                                         [:properties {:secondList ["AAA" "BBB" "CCC"]}])])]
             [:bottom "test"])
```

--

Interesting finding

(slurp file) returns a single string

(clojure.string/split-lines (slurp file)) returns a the lines as a list

I guess this makes sense as a default but was unexpected
