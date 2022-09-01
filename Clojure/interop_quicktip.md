# Interop quick tip

```clojure
(ns yaml-config-manager.manager (:import (java.io File)))
```

```clojure
(defn parts-from-file [^File f]
  (f .)
)
```

As soon as you type the `.` character the autocomplete menu will pop up and give you the options available to the File f.

Note. For right now what I've seen both the import and type-hint are required for autocomplete in Clojure to work, but this is incredibly useful imo as it means that reading the documentation just to see available method names is not required.
