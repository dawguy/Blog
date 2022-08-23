# Files

Running into some issues getting files to be loaded when not using the REPL to directly call the load-files function. Unsure why its not working.

https://stackoverflow.com/questions/72041010/why-the-for-function-is-not-executed

Most likely answer is that for is lazy, and I'm not realizing the values. It appears that instead of for I should be using forv.

```clojure
(vec (for [x [1 2 3] (side-effect! x)]))
```

This worked.
