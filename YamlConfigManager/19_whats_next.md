# What's Next?

Now that the backend and front-end pretty much do what is expected what is next?

## Improved Deployment

Current deployment instructions require code changes to yaml-config-manager to point at the expected target directory.

In addition we need to build + server the angular app.

Idea here is to create a deployment script and/or docker container which will do all of the above for the user. Essentially something like `docker-compose run yaml-config-manager $(pwd)` -d` and it will spin up all the needed parts for us.

## Tests

Could write more unit tests.

## Move Yaml-Config-Manager To Components

If I ever wanted to add database connectivity to this app, moving it to [components](https://github.com/stuartsierra/component) would make it easier to manage.

## Ignored Configs

See incomplete post 15_SQLITE, could implement something like this.
