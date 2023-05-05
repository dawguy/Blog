# Backend

Pretty much just going through the steps I listed in 02. 

https://docs.docker.com/compose/environment-variables/set-environment-variables/
https://hub.docker.com/_/postgres

```txt
Docker Secrets
As an alternative to passing sensitive information via environment variables, _FILE may be appended to some of the previously listed environment variables, causing the initialization script to load the values for those variables from files present in the container. In particular, this can be used to load passwords from Docker secrets stored in /run/secrets/<secret_name> files. For example:

$ docker run --name some-postgres -e POSTGRES_PASSWORD_FILE=/run/secrets/postgres-passwd -d postgres
Currently, this is only supported for POSTGRES_INITDB_ARGS, POSTGRES_PASSWORD, POSTGRES_USER, and POSTGRES_DB.
```

# Driver

Postgresql + component setup as done normally. One small change I'm doing this time is also putting the db into an `(def a-db (atom {}))` to make it easier to do development prior to putting together a webserver.

Hit a Driver missing issue. Just had to add the postgresql driver to the project's dependencies/classpath

# Fun Statement For Generating Data

```clj
    (repeatedly 50 #(sql/insert! (@a-db) :product (mg/generate planogrammed-groceries-backend.product.spec/create-product)))))
```

# Images

https://stackoverflow.com/questions/11321264/saving-an-image-form-clj-http-request-to-file

https://docs.oracle.com/javase/tutorial/2d/images/saveimage.html

Didn't see a :file in Malli. Will either need to write a custom generator or just put images off till later, for now I'm choosing later. But will likely do this soonish.

