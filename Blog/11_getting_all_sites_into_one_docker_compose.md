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
version: "2.2"
services:
  frontend:
    image: bloodisblue/my-blog:1.1.0
    ports:
      - "4200:80"
  backend:
    image: bloodisblue/blog-backend:1.1.0
    ports:
      - "8888:8888"
  kelly-criterion:
    image: bloodisblue/kelly-criterion-client:1.1.0
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

# Docker Hub Setup

Created repositories.

Tagged repositories.
```zsh
docker tag blog-backend bloodisblue/blog-backend:1.0.0
docker tag kelly-criterion-client bloodisblue/kelly-criterion-client:1.0.0
docker tag my-blog bloodisblue/my-blog:1.0.0
```

I expected this to work:
```zsh
docker login
; Logged in above.
docker push bloodisblue/kelly-criterion-client:1.0.0
```

But I got the error Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers). Gonna just use the docker desktop GUI for mac to do this.

---

Oof. Looks like apple m2 causes issues

https://stackoverflow.com/questions/73398714/docker-fails-when-building-on-m1-macs-exec-usr-local-bin-docker-entrypoint-sh

Need to rebuild all images using `FROM --platform=linux/amd64`.

Not fun, but hopefully will work. If this doesn't, then I'll go rebuild them all from my desktop which will most likely be successful even without the platform trick as its already running linux.

# Don't Be Silly

Updated A records on godaddy

But forgot to clear out my /etc/hosts changes >.<. Took me like 20 minutes of clicking around wondering why I could navigate there directly before realizing that I wasn't seeing anything becuase I intentionally set the domain names to localhost...

Good news though is that we successfully deployed everything!

Things left are mostly around the site content and how to use the backend. Like I'll need to also do a redirect of `location /api/` for wrightdavid.com to redirect to the backend service.

Overall took me a couple days to learn how to use docker + nginx, but feel like it wasn't that complex after learning a bit more about how all the pieces worked.
