Files haven't been too bad at all to work wtih.

Pretty much just need to use (def f (clojure.java.io/file "./")) and you have the current directory as a Java file.

Then you can get the contained files with (.list f) or the current file with the slightly more involved 

```clojure
(let [path (str/split (.getAbsolutePath f) #"/")]
                                 (first (drop (- (count path) 2) path)))
```
