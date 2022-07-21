Gotta try this.

https://github.com/eccentric-j/cljs-tui-template

--

Using NPM modules
To use a npm module in ClojureScript use JS interop require:

(def stream (js/require "highland"))

--

Another useful tip is that when a custom macro like reagent/when-let isn't defined. You can right click it and say "use like let". Which then gets rid of the warnings associated with it. Very neat!
