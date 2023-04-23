# NGNIX

Had a couple thoughts on this one at first. 

First try was to just install it directly, but now have decided to try to install it through Docker as I feel like that would make it easier to manage + a good learning opportunity.

## First pass for learning the syntax

```Dockerfile
FROM nginx:latest
 COPY ./nginx.conf /etc/nginx/conf.d/default.conf
 COPY ./assets /assets
 COPY ./data /data
```

```config
server {
     listen 80;
 
     location / {
         root /;
     }
 
     location /a/ {
         root /assets/;
     }
 
     location /d/ {
         rewrite ^/d(.*)$ $1 break;
         root /data/;
     }
 }
```

Some things to note here. The /root/ redirect adds a different root to the request. As seen below this leads to unexpected behavior of /assets/a/1.json.

```txt
2023/04/23 10:52:23 [error] 28#28: *1 open() "/assets/a/1.json" failed (2: No such file or directory), client: 172.17.0.1, server: , request: "GET /a/1.json HTTP/1.1", host: "0.0.0.0"
172.17.0.1 - - [23/Apr/2023:10:52:23 +0000] "GET /a/1.json HTTP/1.1" 404 153 "-" "curl/7.68.0" "-"
```

The fix can be seen for location /d/. Using rewrite we're able to strip the /d portion of the request and end up with something that works as expected.


## Running the container

 To start the container (note that you can add -d to make it run in the background, but for learning I'm finding this version much easier to rerun frequently. Also note that the --rm flag allows us to easily replace this container by auto-removing it when stopped.)
 ```zsh
 docker build -t custom-nginx .
 docker run -it --rm -p 80:80 --name my-custom-nginx-container custom-nginx
 ```
 
 ```zsh
 curl 0.0.0.0:80/assets/1.json
 ```