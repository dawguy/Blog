Essentially lots of learning and debugging has been going on for the last days.
-
Important lessons

When putting FK on child

OneToMany side is mappedBy
CascadeType.ALL is needed by parent AND child

`
@OneToMany(mappedBy = "ergWorkout", cascade = CascadeType.ALL, orphanRemoval = true)
@OrderBy("seq")
private List<ErgSplit> ergSplits = new ArrayList<>();
`

Child-side uses ManyToOne AND JoinColumn. Note: CascadeType.ALL is needed by parent AND child
`
// Note: https://stackoverflow.com/questions/10551485/hibernate-cascade-type
// Cascade type is needed in both Parent and Child.
@ManyToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY)
@JoinColumn(name = "erg_workout")
private ErgWorkout ergWorkout;
`
-
JPA allows you to call save on the top-most parent and it'll save all dependents.
Though as this was the first project lots of small challenges were hit.

The most difficult of which were solved via JPA Entity Lifecycle Events
https://www.baeldung.com/jpa-entity-lifecycle-events

By putting a @PrePersist on the parents, you can explicitly assign the FK's on creation. This fixed some major issues with saves as without this the FK ergWorkout was null.

`
  // https://stackoverflow.com/questions/9533676/jpa-onetomany-parent-child-reference-foreign-key
  @PrePersist
  private void prePersist() {
    if (ergSplits != null) {
      ergSplits.forEach(s -> s.setErgWorkout(this));
    }
`
-
Infinite loops related to bidirectional mappings are a very real thing. In particular I was bitten by the ergWorkout <-> ergSplit.ergWorkout mapping. Note that this is only in terms of serialization from java object to JSON/serialized format via jackson. The values are always accessible from the code which is nice.

Several patterns to resolve this could be found online:
* Multiple DTO's + entities
* Using @JsonIgnoreProperties


ErgSplit.java
`
@JsonIgnoreProperties(
    ignoreUnknown = true,
    value = {"erg_workout", "ergWorkout", "workout"})
`

I chose to go with JsonIgnoreProperties as in my expected case, I should be grabbing ErgSplits by ErgWorkout. So I'm expecting to already know this value at all times so ignoring it shouldn't lead to any future issues.

-

For @Entity classes, you can add fields which are automatically serialized by adding a getter for the method. For example `getWorkout(){return this.ergWorkout}` will cause the serialized JSON to include `workout` as a field.

-
Was not able to determine if there is a way to chain Generics

I had two interfaces

public interface Workout<SplitType> { List<SplitType> getSplits(); }
public interface Split<WorkoutType> { WorkoutType getWorkout();  }


This allowed us to specify precisely what the return types should be while still have generic `getSplits` and `getWorkout` calls known.

public class ErgWorkout implements Workout<ErgSplit> {}
public class ErgSplit implements Split<ErgWorkout> {}

The generics made it far easier to work with, but I was not able to figure a way to specify that a function needs to have the WorkoutType of Workout<ErgSplit> while using them.

-

Builder required both of these patterns:
`
@Builder
@NoArgsConstructor
@AllArgsConstructor
`
-

HibernateProxy

Something that tripped me up quite a bit was that the JPA SQL being sent was looking correct and pulled data, but the the object itself had null in all fields.
This ended up being a debugger only issue as the object created was a HibernateProxy, that as soon as it was accessed would pull the actual values. This occurred because we used Lazy fetching.
https://stackoverflow.com/questions/8945365/jpa-hibernate-proxy-not-fetching-real-object-data-sets-all-properties-to-null

"hat is probably what you saw in a debugger, right? You don't have to worry about that, once you access any of those properties via code or via a call inside the debugger, Hibernate will load the data from the DB in the background, construct an instance of your entity class and all calls to the proxy object will be delegated transparently to the actual entity. So normally, and ideally you don't have to care about the distinction Hibernate proxy <-> entity object."

-

Unresolved issue: PreUpdate not being called

An issue that I wasn't able to resolve was the PreUpdate not being called when I made a call to waterWorkoutRepostiory.save(waterWorkout) when adding splits to a workout (in this case the target was WaterWorkoutAthleteSplit that had a null pointer to WaterWorkout FK).

The original plan was that this save would work similarly to the initial creations and would allow me to permanently avoid nulls with a loop that sets the FK to this (from the caller being saved). However I was unable to figure out how to get this to be ran and decided to just explicitly set the FK as all the information was known ahead of time.

This problem likely will appear again in the future, though the future solution is also very likely going to also be explicitly set the FK.
