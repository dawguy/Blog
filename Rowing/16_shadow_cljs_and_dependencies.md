Small couple things to remember for the future.

Join the REPL via the following 2 commands:

(shadow/watch :app)
(shadow/repl :app)

-

Cursive has a really cool feature that lets you automatically choose the port based on the nREPL file generated when starting shadow/server.

This is very convenient since I no longer need to manually copy+paste the port every time.

-

(Open since 2017) Cursive github issue for adding support for shadow-cljs:
https://github.com/cursive-ide/cursive/issues/1804

Pretty much it seems like adding support for shadow-cljs is going to be a ton of work and won't ever be done.

Workaround by thheller:
https://github.com/thheller/shadow-cljs/issues/70

`npx shadow-cljs pom`

Essentially whenever a new dependency is added, in order to get it to be understood by intellij you will need to re-generate the pom.xml. This isn't used by shadow, but is understood by cursive and will allow all the autocomplete features to be used.
