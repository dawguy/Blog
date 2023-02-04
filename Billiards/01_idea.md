# Idea

I want to create a billiards game & bar style simulation in clojure.

## Goals 

Improve parallelism and interop understanding through this project.

Rough ideas on how to do each of these.
* Parallelism
** Either encapsulate each Table object as something that can be distributed for computation or do the simulation as a set of balls that each operate independently and need to run into each other.
** Also could add a bracket/tournament/queue to play and have something or multiple things manage who is playing.

* Interop
** I'm expecting the step calculation to be fast enough as pool has 15 balls and that isn't a particularly huge amount of boxed math to do, but could be fun to do this step and all calculations in Java just to get the hang of interop.

## Stretch Goals

* Implement UI for the game. Goal would be to make the UI browser based.
* Add AI for the gameplay. Particularly fun would be ML that plays pool and tries to get better by playing many games across many tables.
* Implement the table system and players can enter/exit tables with real time interaction via websockets.
