# Workouts

Pretty much the only way I can see this making sense is to add something which adds a split onto a workout.


For the future things I'd want.

* To give a recommended power percentage based on FTP
* Recommended stroke rate for water workouts.
* Denote workout as template allowing it to be assignable
* Reorder the sequences
* Auto-generate water workouts from path?

## Recommended power and stroke rate
I'm pretty certain that giving recommended power and stroke rate could both be done via additional fields on the table itself.

## Denote workout as template

I guess this comes to the question is the workout library going to be the same system or a different system.

Benefits of sharing system. The sequencing logic between the two is near identical. Easier to create a fill-in-the-blank version if they share the same underlying table.
Downsides. Not all fields would be usedby either one.

Feeling like sharing makes the most sense.
---
Essentially the default will be to create workouts, then assign out to athletes/boats. And the assigned out versions would be what actually have values added.
---
Should templates have sub-templates?

Aka, 20x 40 on 20 off. Would we want variationsj:w

Do we want loops? That would be cool, but might just make that a UI thing, with the DB
---
Workouts need to handle the ability to define either duration or distance. For example a 10k row might be put into a WaterSplit
---
What else might they need?

I'd really like a way to do something like this

Template Workout
-- Assigned Workout
  -- Per Athlete/Boat Workout


That way its very easy to say here is the one we are attempting to do. And here is the one that all the athletes can be tagged too for a specific date. Then each athlete who performs the workout gets a score.
-
Would that give an easy way to simulate later?

maybe.
---

