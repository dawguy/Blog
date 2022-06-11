I want to be able to quickly load a large set of gitlab repositories in chrome without having to use the UI.

This will save me lots of time in the near future and sounds like a fun project.

Essentially the goal will be to have a bash + babashka program intelligently look at the current folder and determine if we're in a microservice (in which case prompt for that microservice) or a container of microservices (in which case prompt for which one you want to open).

And then give an extra option in case you want to go to the MR, Environment, or Pipeline page.

---

Seemed simple, seems like fun to make, and seems like it would be a good introduction to CLI.

In addition since I would like to use this for work, I want to provide a sqlite file which is read from rather than in-memory lists like I've used in other projects.

I'd also like to get some basic tests up and working.

---

How to run the clojure portion
```sh
bb src/microservice_navigator.clj
clj src/microservice_navigator.clj -X microservice-navigator/run
```

Sample output
```txt
➜  microservice-navigator git:(main) ✗ bb src/microservice_navigator.clj
"-----------------------------------------"
"Select the microservice you wish to open:"
1: All
2: https://github.com/dawguy/websockets-tictactoe
3: https://github.com/dawguy/websockets-tictactoe-client
2
"-----------------------------------------"
"Select the page you want to open:"
1: Home
2: Issues
3: Pull Requests
4: Actions
1
```
