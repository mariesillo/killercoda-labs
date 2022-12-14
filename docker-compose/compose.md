## Compose sample application
### NGINX proxy with GO backend

Lets clone our repo first:

```
git clone https://github.com/mariesillo/nginx-golang.git
cd nginx-golang
```{{exec}}


Project structure:
```
.
├── backend
│   ├── Dockerfile
│   └── main.go
├── compose.yaml
├── frontend
│   ├── Dockerfile
│   └── nginx.conf
└── README.md
```

compose.yaml
```
version: "3.7"
services:
  proxy:
    image: nginx
    volumes:
      - type: bind
        source: ./proxy/nginx.conf
        target: /etc/nginx/conf.d/default.conf
        read_only: true
    ports:
      - 80:80
    depends_on:
      - backend

  backend:
    build:
      context: backend
      target: builder
```
The compose file defines an application with two services `frontend` and `backend`.
When deploying the application, docker-compose maps port 80 of the frontend service container to the same port of the host as specified in the file.
Make sure port 80 on the host is not already in use.

## Deploy with docker-compose

Start Services


```
docker-compose -f compose.yaml up -d
```{{exec}}
Results:

```
$ docker-compose -f compose.yaml up -d
Creating network "nginx-golang_default" with the default driver
Building backend
Step 1/7 : FROM golang:1.13 AS build
1.13: Pulling from library/golang
...
Successfully built 4b24f27138cc
Successfully tagged nginx-golang_frontend:latest
WARNING: Image for service frontend was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Creating nginx-golang_backend_1 ... done
Creating nginx-golang_frontend_1 ... done
```

## Expected result

Listing containers must show two containers running and the port mapping as below:

```
docker-compose -f compose.yaml ps
```{{exec}}

Results:

```
$ docker-compose -f compose.yaml ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                  NAMES
8bd5b0d78e73        nginx-golang_frontend   "nginx -g 'daemon of…"   53 seconds ago      Up 52 seconds       0.0.0.0:80->80/tcp     nginx-golang_frontend_1
56f929c240a0        nginx-golang_backend    "/usr/local/bin/back…"   53 seconds ago      Up 53 seconds                              nginx-golang_backend_1
```

After the application starts, navigate to `http://localhost:80` in your web browser or run:

```
curl localhost:80
```{{exec}}
Results:

```
$ curl localhost:80

          ##         .
    ## ## ##        ==
 ## ## ## ## ##    ===
/"""""""""""""""""\___/ ===
{                       /  ===-
\______ O           __/
 \    \         __/
  \____\_______/


Hello from Docker!
```

[Golang Application]({{TRAFFIC_HOST1_80}})

Stop and remove the containers

```
docker-compose -f compose.yaml down
```{{exec}}
Results:

```
$ docker-compose down
Stopping nginxgolang_frontend_1 ... done
Stopping nginxgolang_backend_1  ... done
Removing nginxgolang_frontend_1 ... done
Removing nginxgolang_backend_1  ... done
Removing network nginxgolang_default
```
