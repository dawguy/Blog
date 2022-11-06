# Components

Pretty much 2 things here:

System -> when start is called on this it returns the system, and recursively calls start on all dependencies.

Component -> this is a single thing, contains state and is part of a system.

## System

Created via system-map
```clojure
(component/system-map :application (my-application {:repl repl})
                         :database    (model/setup-database)
                         )
```

Started as a system
```clojure
  (alter-var-root #'system component/start)
```

Individual pieces can be grabbed
```clojure
  (def system (new-system 8888))
  (:application (system))
```

## Component

Needs a defrecord describing the component.

```clojure
(defrecord Application [config   ; configuration (unused)
                        database ; dependency
                        state]   ; behavior
  component/Lifecycle
  (start [this]
    ;; Component ensures that dependencies are fully initialized and
    ;; started before invoking this component.
    (assoc this :state "Running"))
  (stop  [this]
    (assoc this :state "Stopped")))

(defn my-application [options]
  (component/using (map->Application options)
                   [:database]))
```

In particular this is how you define the dependency chain
```clojure
  (component/using (map->Application options)
                   [:database])
```

