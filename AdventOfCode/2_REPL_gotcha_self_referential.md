# Self Referential Gotcha


```clj
(defn parse-lines [lines apply-fn?]
  (if (not apply-fn?)
    lines
    (if (sequential? lines)
      (map #(parse-lines % apply-fn?) lines)
      (or (try
            (Integer/parseInt lines)
            (catch NumberFormatException e nil))
          (try
            (Double/parseDouble lines)
            (catch NumberFormatException e nil))
          lines))))
```

While working on the above block of code I hit some issues using the REPL. It ended up that since I was referring to (fn parse-lines), normal repl development required the function to be redefined.

I actually don't think that there is a simple solution to this. As I tried `#(#'parse-lines % apply-fn?')` as that has helped in the past for similar problems. But this one is different as the function being referred to wasn't a stale reference, but hadn't been sent to the REPL as the definition yet.

A loop-recur would've been better than self referential function I believe to avoid this.
