# Tests

Key notes for testing. 

Adding hotkeys for running all tests in file and for running test under carrot in current REPL are incredily useful. Without these writing tests would likely be a huge chore. One thing to note is that the feedback does feel a bit slower than normal REPL development.

I'll probably want to practice a bit more writing TDD as part of normal REPL development since it would make writing tests near 0 effort.

## Float Equality

Float equality (and vector comparisons) required additional work.

```clojure
(defn rel= [a b epsilon]
  (<= (abs (- a b)) epsilon))
(defn v-rel= [a b epsilon]
  (and
    (rel= (:x a) (:x b) epsilon)
    (rel= (:y a) (:y b) epsilon)))
```

However these weren't quite enough as the expected + actual results looked really weird when tests failed. They took the form itself rather than the calculated values. After some digging I found that this was an edge case clojure.test accounted for. 

By overloading the assert-expr function and providing the name of the method you could redefine the behavior.

(defmethod assert-expr 'rel= [msg form]

```clojure

(defn assert-rel-predicate
  "Base function copied from assert-predicate. Takes the expression in format
  (is (rel= (result-fn) expected epsilon). Then returns the result of result-fn in :actual and expected for :expected"
  [msg form]
  (let [args (rest form)
        pred (first form)
        exp (second args)]
    `(let [values# (list ~@args)
           calc-result# (first values#)
           bool-result# (apply ~pred values#)]
       (if bool-result#
         (do-report {:type     :pass, :message ~msg,
                     :expected '~exp, :actual calc-result#})
         (do-report {:type     :fail, :message ~msg,
                     :expected '~exp, :actual calc-result#}))
       bool-result#)))

(defmethod assert-expr 'rel= [msg form]
  (if (and (sequential? form) (function? (first form)))
    (assert-rel-predicate msg form)
    (assert-any msg form)))
(defmethod assert-expr 'v-rel= [msg form]
  (if (and (sequential? form) (function? (first form)))
    (assert-rel-predicate msg form)
    (assert-any msg form)))
```

At the end of the day this is what the macro I wrote ended up looking like. It successfully reported the actual and expected values in {:x 123, :y 123} format but only when ran from the command line. The clickable banner Cursive put for test failures didn't work. I'm leaving that as a to-do.
