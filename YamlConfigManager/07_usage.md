# Thought of the ui I want

Commands enter editing session mode. Which pops up 3 windows. 2 containing the yaml itself being edited in both services. The bottom is a selection mechanism for quickly comparing the selections.

---------------------------------------------------
| watch: Staging/serviceA | watch: Prod/seviceA   |
| abc:                    | abc:                  |
|   def: 123              |   def: 'abc'          |
|   ghj: 345              |   ghj: 999            |
|                         |                       |
|                         |                       |
|                         |                       |
|                         |                       |
|                         |                       |
|                         |                       |
---------------------------------------------------
| Comparing property `abc.def`                    |
|                                                 |
| Values:                                         |
| ServiceA Staging: 123                           |
| ServiceA Prod: 'abc'                            |
|                                                 |
| 1. Staging: 123                                 |
| 2. Prod: 'abc'                                  |
| 3. Leave as is                                  |
|                                                 |
---------------------------------------------------


The way I know how to do this already would be something like

htop. Split screens.

watch cat on two files that the main program creates + updates in /tmp.

Bottom screen would be controlled with clear/print lines.

-

From some research https://unix.stackexchange.com/a/146491 it appears that termianls are character-cell displays that don't support pixel graphics.

But there are UI toolkit libraries that interact with the X server! Which makes it possible to do pretty much anything you desire.

-

https://clojurebridgelondon.github.io/workshop/introducing-clojure/rich-terminals.html

Seems like https://github.com/eccentric-j/cljs-tui-template https://eccentric-j.com/blog/5-announcing-cljs-tui-template.html eccentric-j create a clojure terminal ui farmework which parses hiccup. This seems absolutely incredible and I want to go try it out!

Ran into a small issue trying this out. Essentially with shadow-cljs (or any external repl) the IntelliJ option for Local REPL is incorrect. You really want to connect to an external REPL that is running the project when you're running the REPL from a separate terminal instance.

Was hitting this exception which prompted me to investigate.
```log
Caused by: java.io.FileNotFoundException: Could not locate cljs/analyzer/passes/and_or__init.class, cljs/analyzer/passes/and_or.clj or cljs/analyzer/passes/and_or.cljc on classpath. Please check that namespaces with dashes use underscores in the Clojure file name.
	at clojure.lang.RT.load(RT.java:466)
```

These were necessary as always to connect and change from a clj REPL to cljs REPL.
```clojure
(shadow/watch :app)
(shadow/repl :app)
```

---

Seems like there are ways to start the server & the UI from a single bash script. So the idea for now is going to be have a local web-server running containing the YAML data and the UI running which queries the webserver.

https://stackoverflow.com/questions/3004811/how-do-you-run-multiple-programs-in-parallel-from-a-bash-script

---

To start nREPL
npx shadow-cljs watch app

To start terminal UI session
node --inspect target/js/compiled/yaml-config-manager-ui
