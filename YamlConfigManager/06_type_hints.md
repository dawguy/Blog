# Type Hints

```clojure
(ns yaml-config-manager.manager
  (:import (java.io File))
  )


(def ^File fs file-seq f)
(-> fs (first) )
(-> f
    (.isDirectory))
(.isFile f)
(.isFile f)
(-> f (.))
(-> f
    (.isFile))
(. f isFile)
(.. f isFile)

(-> f
    (.isDirectory))
(. f isDirectory)
(.. f isDirectory)
```

Interestingly cursive is giving me very interesting results.

Without a type hint it works as expected, but the autocomplete is rough.

Without the import the File methods don't appear at all.

The only way to get the autocomplete easily working in cursive was (-> f (.is)) format. This would give an autocomplete suggestion of all available functions based on f.

Note: Those autocomplete suggestions were reliant on both the type hint on f and the :import of (java.io File). If either of those were missing no suggestions were given.

Out of the two I'd find that the import is more imporant as even without a typehint the import added the ability to see .isFile and .isDirectory as autocomplete options.

---

Hopefully I'll be able to find more useful ways to learn/use the autocomplete features as I work on interop based features.
