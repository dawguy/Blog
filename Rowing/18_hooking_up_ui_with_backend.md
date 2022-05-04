Now that I have the ability to make requests, save data to app-db, and read the URL of the current page. The end goal of my next couple commits will be to get the page contents dynamically loading.

-

The first thing that I forgot to do was deref the atom.

Essentially I made the page based on the atom with something that looks like below. The lack of derefing the atom meant that reagent didn't auto-detect changes.

https://cljdoc.org/d/mthomure/reagent/0.8.1-custom-components/doc/frequently-asked-questions/why-isn-t-my-component-re-rendering-

```clojure
(defn page [data]
  (prn data)
  (case (:target data)
    :ergWorkout (workoutContainer (get-in data [:ergWorkout (:targetId data)]))
    (home)))
```

```clojure
(defn page [data]
  (prn @data)
  (case (:target @data)
    :ergWorkout (workoutContainer (get-in @data [:ergWorkout (:targetId @data)]))
    (home)))
```

```clojure
(defn page [data]
  (prn @data)
  (case (:target @data)
    :ergWorkout (workoutContainer (get-in @data [:ergWorkout (:targetId @data)]))
    (home)))
```

---

Something that's been very frustrating for me is how to get the ratoms to update the UI automatically.

So far I've explored a little and will list the options I've tried so far.

Swap from[:div (page app-db)] to [:div [page app-db]] along with returning a render function in page rather than just a single component.

I think this is what allowed it to start working.  In addition to correctly derefing the atom.

---

For right now I think I'm okay with combining the loading + saving + swapping pages functionalities into one big function.
