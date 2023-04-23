# Dockerizing Angular

## Terminology
Something to note:

RUN = command done as part of build process
CMD = command ran when image is booted
ENV = Set environment variable for future containers
ARG = Set environment varibale for Dockerfile build

## Getting into the application

This command lets us open the docker image with a custom entrypoint instead of the CMD.
https://stackoverflow.com/questions/43308319/how-can-i-run-bash-in-a-new-container-of-a-docker-image

```zsh
docker run --rm -it --entrypoint bash my-blog
```

Useful for debugging.

## Turning off angular warning

https://stackoverflow.com/questions/56355499/stop-angular-cli-asking-for-collecting-analytics-when-i-use-ng-build

## Basic Dockerfile I Made


```Dockerfile
FROM node:latest
ENV NG_CLI_ANALYTICS=ci

RUN mkdir -p /app
WORKDIR /app

COPY . /app
RUN npm install @angular/cli
CMD npx ng serve --host 0.0.0.0
```

```zsh
docker build -t my-blog
docker run -it --rm -p 4200:4200 --name my-blog my-blog
docker run --rm -it --entrypoint bash my-blog
```

## More notes

Seems like the angular deployment itself can be extremely small. Just needs the output of the `ng build` and for the server to be setup to redirect requests to /index.html if not found. The Angular router will handle it from there.

https://github.com/docker/awesome-compose/blob/master/angular/angular/Dockerfile

https://angular.io/guide/deployment

https://angular.io/guide/deployment#fallback (In particular here)


https://phoenixnap.com/kb/docker-environment-variables -- Env vs arg

https://dev.to/this-is-angular/multi-stage-docker-builds-with-angular-and-nginx-4j9d

## Attempt 2: Making It Smaller

https://dev.to/this-is-angular/multi-stage-docker-builds-with-angular-and-nginx-4j9d

Will be building the app, then copy+pasting the built app to a tiny nginx image instead for hosting.

Having some issues with this approach. Mostly arouned getting a valid nginx config which also displays the app.

https://github.com/nginxinc/docker-nginx/blob/4e5332fa50a1f8f73657417c6bfe249bbb3b110d/Dockerfile

Seems like daemon off is needed. Otherwise it won't run, guess this makes sense as the daemon is only really useful when you want to run something not in the foreground, and docker doesn't really need to do this at all.

```Dockerfile
FROM node:latest AS builder

RUN mkdir -p /app
WORKDIR /app
COPY package.json package-lock.json .

RUN npm install --legacy-peer-deps

COPY . .

RUN npm run build --  --output-path=dist --output-hashing=all

FROM nginx:stable-alpine
EXPOSE 80
RUN rm -rf /usr/share/nginx/html/*
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf
COPY --from=builder /app/dist /usr/share/nginx/html

CMD ["nginx", "-g", "daemon off;"]
```

```conf
events {
  worker_connections 1024;
}

http {
  server {
    server_name localhost
    listen 80;

    location / {
      #### Gzip Settings  ####
      gzip on;
      gzip_min_length   1100;
      gzip_vary         on;
      gzip_proxied      expired no-cache no-store private auth;
      gzip_types        text/plain text/css application/json application/javascript application/x-javascript text/xml    application/xml application/xml+rss text/javascript;
      gzip_comp_level   5;

      #### Serve Angular Application ####
      root /usr/share/nginx/html;
      try_files $uri $uri/ /index.html;
      proxy_http_version 1.1;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Forwarded-Port $server_port;
    }
  }

  include /etc/nginx/mime.types;
  default_type application/octet-stream;
}
```

```zsh
docker build -t my-blog .
docker run -it --rm -p 4200:80  --name my-blog my-blog
```

Ending code! One thing to note is that the final things which stumped me a bit were the daemon off, and then I originally didn't have mime.types include causing failures on loading the .js assets.
