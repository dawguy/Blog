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

---

Ended up that I liked separating out the pieces. Now I save and load each separate db entity directly from app-db. With some helper functions added to handle nested elements such as splits or waterWorkoutAthleteSplit

---

Another very important thing is that when referring to javascript properies. The -{property} must awlays be used.

https://lwhorton.github.io/2018/10/20/clojurescript-interop-with-javascript.html

This is important to remember as (.. js/window -location -href) works, but (.. js/window location href) does not.

This is because - looks at properties while leaving it off attempts to call as if its a function.

---

Overall I found getting everything hooked up not too bad seeing that all the componenets and router matching parts were already done. All it took was a little putting the pieces together.
