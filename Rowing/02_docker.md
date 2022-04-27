# Postgres Installation
## Docker Tutorials
https://www.youtube.com/watch?v=aHbE3pTyG-Q

https://hub.docker.com/_/postgres

## Docker Steps

Download the image
docker pull postgres:alpine

Start up the postgres conatiner
docker run --name some-postgres -e POSTGRES_PASSWORD=passwordHere -d -p 5432:5432 postgres

Start up a bash shell where I can log into postgres using the above password and create any databases and tables I may need.
docker exec -it some-postgres bash

Start up the postgres container whenever I restart my local computer
docker start some-postgres

## Problem shooting
At first try, the container's postgres was failing. This ended up being due to the port 5432 already being used by the non-dockerized version of postgres I had on my system.


`ss -nlp | grep 5432`
```
u_str              LISTEN              0                    244                                                   /var/run/postgresql/.s.PGSQL.5432 92050                                                 * 0                                    users:(("postgres",pid=10293,fd=6))
tcp                LISTEN              0                    244                                                                           127.0.0.1:5432                                            0.0.0.0:*                                    users:(("postgres",pid=10293,fd=5))
```

`systemctl list-units --all | grep "postgres"`
```
postgresql.service                                                                         loaded    active     exited    PostgreSQL RDBMS
  postgresql@13-main.service                                                               loaded    active     running   PostgreSQL Cluster 13-main
  system-postgresql.slice                                                                  loaded    active     active    system-postgresql.slice
```

After stopping the postgres service with `sudo service postgresql stop` I was able to successfully connect!
