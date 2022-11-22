# What's Next

Now that the ability to insert into tables has been implemented what is next?

## Foreign Key Chained Creates

There needs to be a system in place which allows data to be generated in a way that allows parent-child or sibling-sibling relationships. An idea for how to do this is to first do a topological dependency sort like the component library, then pass an atom from the parent to the children and allow each child to share its PK fields with the atom.

This feels like the most flexible approach, and won't have issues with parallelism due to the interworkings of atoms.

## Simplification Of Table Defintions

Having to manually specify each and every table's definition and creates/deletes is somewhat tedious. Creating a macro that defines these things based off a simpler map definition would be ideal. 

## Database Switching
As part of the above effort, being able to specify what database type is being used more easily would be a nice addition too. Right now I'd like my macbook to do SQLite for both tracking table and data table while desktop to do SQLite for tracking table but data table in Cassandra.

## Tracking Table Based Deletes
This was always planned, but hasn't been implemented yet.
