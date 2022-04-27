Attempts to set up the database.

Currently I have reused the functional option pattern for Database parameter setup (https://www.sohamkamani.com/golang/options-pattern/). However I am unsure how to actually use this.

Things I'm thinking about
* Do I understand the difference betweeen a DB and a Conn in Golang?
* Do I want some type of connection pooler and close it at the end
* Am I okay with opening a connection per query.
* How do I want to divide the business logic of the queries from the database definition. 
* What do I not already know about this type of logic.

I'll start by looking into the sql golang documentation

## Do I understand the difference between a DB and a Conn in Golang
`type Conn`
"Conn represents a single database connection rather than a pool of database connections. Prefer running queries from DB unless there is a specific need for a continuous single database connection."
https://pkg.go.dev/database/sql#Conn


`type DB`
"DB is a database handle representing a pool of zero or more underlying connections. It's safe for concurrent use by multiple goroutines."
https://pkg.go.dev/database/sql#DB

That makes it easier to decide what to do. I want to open a DB instance and have some type of methods which allow other parts of my app to make requests to it. Interestingly, golang doesn't provide any native database drivers, so I will be making use of the github.com/jackc/pgc package.

## Thoughts That Have Been Answered By Looking At Documentation
* Do I want some type of connection pooler
* Am I okay with opening a connection per query and close it at the end.

The default DB represents a pool. This makes it very straightforward and the answer is that I get both of these pretty much by default.


## What I didn't think about
* Certs

## Getting a basic connection instantiated
This blog post was incredibly helpful when combined with the pgx documentation https://www.alexedwards.net/blog/organising-database-access and https://github.com/jackc/pgx/wiki/Getting-started-with-pgx.


