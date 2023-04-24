# Docker Compose
Docker compose writing was pretty straight forward.

## /etc/hosts
One thing I did do for testing was add these to `/etc/hosts`

```txt
127.0.0.1   yoloresponsibly.com
127.0.0.1   wrightdavid.com
```

# Debugging.

```log
reverse-proxy                  | 172.18.0.1 - - [24/Apr/2023:15:06:37 +0000] "GET / HTTP/1.1" 502 559 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36"
reverse-proxy                  | 2023/04/24 15:06:37 [error] 29#29: *1 connect() failed (111: Connection refused) while connecting to upstream, client: 172.18.0.1, server: wrightdavid.com, request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:4200/", host: "wrightdavid.com"
reverse-proxy                  | 2023/04/24 15:06:37 [error] 29#29: *1 connect() failed (111: Connection refused) while connecting to upstream, client: 172.18.0.1, server: wrightdavid.com, request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:4200/", host: "wrightdavid.com"
reverse-proxy                  | 2023/04/24 15:06:37 [error] 29#29: *1 no live upstreams while connecting to upstream, client: 172.18.0.1, server: wrightdavid.com, request: "GET /favicon.ico HTTP/1.1", upstream: "http://localhost/favicon.ico", host: "wrightdavid.com", referrer: "http://wrightdavid.com/"
```

Seems like this is a common problem and as expected is because the nginx docker reverse proxy is talking to itself.

https://stackoverflow.com/questions/38346847/nginx-docker-container-502-bad-gateway-response

Seems like 2 possible solutions:
* --net=host this flag says run the container on same network as the host. So we can talk with ports as expected
* Refer to the remote server IP directly

https://stackoverflow.com/a/52532857

Can get ip address with this.
```zsh
ifconfig | grep -Eo 'inet (addr:)?([0-9]*\.){3}[0-9]*' | grep -Eo '([0-9]*\.){3}[0-9]*' | grep -v '127.0.0.1' | head -n 1
```

# Working setup!

```conf
events {}

http {
 server {
     server_name yoloresponsibly.com;
     listen 80;
     listen [::]:80;

     location / {
         proxy_pass http://10.0.0.107:8001;
     }
 }

 server {
     server_name wrightdavid.com;
     listen 80;
     listen [::]:80;

     location / {
         proxy_pass http://10.0.0.107:4200;
     }
 }
}
```

```docker-compose
 version: "1.0"
 services:
   frontend:
     image: my-blog:latest
     ports:
       - "4200:80"
   backend:
     image: blog-backend:latest
     ports:
       - "8888:8888"
   kelly-criterion:
     image: kelly-criterion-client
     ports:
       - "8001:80"
   reverse-proxy:
     image: nginx:latest
     container_name: reverse-proxy
     volumes:
       - ./reverse-proxy/nginx.conf:/etc/nginx/nginx.conf
     ports:
       - "80:80"
       - "443:443"
```
