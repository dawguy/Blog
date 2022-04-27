https://ghufran.posthaven.com/setting-up-a-reagent-clojurescript-project-with-shadow-cljs-and-cursive

https://www.rockyourcode.com/tutorial-clojurescript-app-with-reagent-for-beginners-part-1/

https://github.com/sophiabrandt/breaking-bad-quotes

--
Essentially my plan is to use shadow-cljs, because from what I can read the author is awesome and really in-tune with the community.

Plus it makes stuff like running a nREPL server really easy.

--

Was planning on having a "learning" project, but f it. We doing this live and gonna be building out the rowing website as I learn. Hopefully this will be fun :D

https://eccentric-j.com/blog/5-announcing-cljs-tui-template.html

This guy seems to have a lot of really good tips

--

https://shadow-cljs.github.io/docs/UsersGuide.html#build-repl

How to start watch from nREPL

Key is:
terminal:
npx shadow-cljs server

To start the nREPL & server.

Then in intellij connect to the nREPL
then
(shadow/watch :app)
(shadow/repl :app)


REPL how it makes life so much better
https://cognitect.com/blog/2017/6/5/repl-debugging-no-stacktrace-required

-

Fantastic sample application
https://github.com/jacekschae/conduit

-

https://github.com/noprompt/garden

-

How hot-reloading works. Since I've been a bit stumped trying to alter atoms and see the results be propagated by reagent.

https://code.thheller.com/blog/shadow-cljs/2019/08/25/hot-reload-in-clojurescript.html

-

Interested in tailwindcss since it also looks fun. 
https://curiousprogrammer.dev/blog/how-can-i-use-tailwind-in-my-clojure-script-web-app/
led to https://github.com/jacekschae/shadow-cljs-tailwindcss

Needed to install more recent node first though
https://github.com/nvm-sh/nvm

Also needed to install npm-run-all for the run-p command.

Overall the ability to run both processors at once from the shadowcljs + tailwindcss-jit project is kinda dope.

-

Note: tailwindcss is hard. Even when using component kits.

https://stackoverflow.com/questions/1274057/how-can-i-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitign

Used this on main.css

---

Essentially reagent + tailwindcss makes react + clojurescript really easy to use.

I found https://tailwindcomponents.com/ for components that are usable. And then did a good amount of tweaking.

For charts somebody made https://rollacaster.github.io/hiccup-d3/ which was great for allowing me to use d3 charts with no issues.

---

Some difficulties have primarily been around deciding what to make and styling. The clojurescript parts actually feel like the straight-forward areas. The only mistake I've really made so far is something which looks like this

```
(defn do-thing [w]
(let [splits (:splits w)]
    (map (fn [s]
    ...
    [:tr
        (get-duration splits)
        (get-power splits)
    ]
    ...
    ) (add-field-to-splits w)) 
))
```

Essentially I was attempting to map a field to each split then call a function. This was causing bugs because inside the function I was referring to the split defined in the (let) function.

---

Overall I'd say that my favorite part of clojurescript is that once you start doing something, its incredibly easy to get into the state of flow.

The REPL and shadow-cljs/repl like front-end hot-reloading makes it so easy to get hyperfocused compared to a language like Java.

It doesn't make learning new things like TailwindCSS styling easier, or deciding what to do next easier. But its fun which keeps me coming back and actually doing something every day which can't be said about go.

Especially since with clojurescript I can see a very good usecase every time. For clojure it'll be a little harder since I don't have too many back-end projects in mind but will attempt to keep playing with this since its fun.
