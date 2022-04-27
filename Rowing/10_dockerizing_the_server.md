Creating the dockerfile was surprisingly easy
https://www.baeldung.com/dockerizing-spring-boot-application

-
Commands to start it

`
mvn clean package
docker build --tag=rowing-server-java:latest .
docker run --name rowing-server-java -p8080:8080 rowing-server-java:latest
docker container start rowing-server-java
`

I did hit an issue with connecting to the postgres server
https://stackoverflow.com/questions/37482716/using-docker-to-launch-web-app-cant-connect-to-postgresql-db

My solution was insteadof localhost/5432 to run

hostname -I

And select the hostname of the desktop machine 172.17.0.1, which did have a port opened as expected

-

Easy copy+paste

`docker container start some-postgres & docker container start rowing-server-java`
