# Building Cassandra

## Docker

Moving parts that I'm using.

* Docker
* Dockerfile
* Docker-compose

## Why Dockerfile?

Allows replacing the cassandra.yaml file with a very simple set of commands. Without the Dockerfile allowing materialized views wouldn't be possible.

```docker
FROM cassandra:3.11.2
ENV LANG C.UTF-8
COPY ./cassandra.yaml /etc/var/aaa/cassandra.yaml
```

```docker-compose
version: '3'

services:
  my-cassandra:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: my-cassandra
    ports:
      - "9042:9042"
    restart: always
    environment:
      - "MAX_HEAP_SIZE=256M"
      - "HEAP_NEWSIZE=128M"
      - CASSANDRA_PASSWORD=cassandra
    volumes:
      - /Cass/data:/var/lib/cassandra
    healthcheck:
      test: [ "CMD", "cqlsh", "-u cassandra", "-p cassandra" ,"-e \"describe keyspaces\"" ]
      interval: 15s
      timeout: 10s
      retries: 10

  cassandra-load-keyspace:
    container_name: cassandra-load-keyspace
    image: cassandra:3.11.2
    depends_on:
      - my-cassandra

volumes:
  cassandra_data:
    driver: local
```

## Useful Commands

Start the cassandra container
`docker-compose up`

Load /bin/bash in cassandra's container
`docker exec -ti my-cassandra /bin/bash`

Rebuild the images (allowing changes to cassandra.yaml to be picked up)
`docker-compose build`

## Warnings

Not 100% sure how the image is cached, but changes to cassandra.yaml don't always make it to the container after it gets started.

Trying to figure out what needs to be done now.

Ideas:
* Clear cache
* Rebuild

Right now trying out `docker-compose build` then rerunniung the `docker-compose up`.

This got it working! Adding this to the above useful commands section.

## Goals

Goal is to create an easy way to start up the cassandra db for basic testing without having to worry about doing this manually every time.
