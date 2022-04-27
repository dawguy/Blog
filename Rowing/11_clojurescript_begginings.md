ClojureScript - The begginings!

https://clojure.org/guides/getting_started
https://cursive-ide.com/

---

Too basic:

https://www.toptal.com/clojure/clojurescript-tutorial-react-front-end


Uses https://github.com/reagent-project/reagent & https://github.com/weavejester/hiccup for parsing clojurescript to react
CSS: https://github.com/clj-commons/cljss

---

Of course the langauge's quickstart would be ideal
https://clojurescript.org/guides/quick-start

Repl needs to come at the end
clj -M --main cljs.main --compile hello-world.core --repl


For production optmiized dead-code removal:
clj -M -m cljs.main --optimizations advanced -c getting_started.core

Then
clj -M -m cljs.main --serve

---
Ends up that all these tutorials are kinda rough. The first resource that has made learning fun so far has been the reagent-project's info page. https://reagent-project.github.io/

Everything else uses javascript interop which results in errors and doesn't autocomplete in my IDE.

-
-Note: Tried downloading emacs as well :D, seems like a neat text editor.
-

reagent seems like the ideal way to learn this stuff.

Now I'm reading about two different build tools. deps.edn and shadow-cljs. Gonna try out shadow-cljs since it seems like it integrates with npm better (not that I have many plans to use npm.)

-
Overall enjoyed the playing around with figwheel while learning reagent. Gonna now try to do a very basic site as a random shadow-cljs project using reagent, then pop over to trying to make Rowing.
