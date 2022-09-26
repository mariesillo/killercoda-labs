---
layout: post
title:  "Docker Compose Example Application"
date:   2021-04-26
tags: [Linux,operations]
categories: other
---


## Compose sample application
### NGINX proxy with GO backend

Lets clone our repo first:

```.term1
git clone https://github.com/docker/awesome-compose.git
cd awesome-compose/nginx-golang
```


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
services:
  frontend:
    build: frontend
    ports:
      - 80:80
    depends_on:
      - backend
  backend:
    build: backend
```
The compose file defines an application with two services `frontend` and `backend`.
When deploying the application, docker-compose maps port 80 of the frontend service container to the same port of the host as specified in the file.
Make sure port 80 on the host is not already in use.

## Deploy with docker-compose

Add version at the top of compose file


```.term1
sed -i '1s/^/version: "3"\n/' compose.yaml  
```

Start Services


```.term1
docker-compose -f compose.yaml up -d
```
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

```.term1
docker ps
```

Results:

```
$ docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                  NAMES
8bd5b0d78e73        nginx-golang_frontend   "nginx -g 'daemon of…"   53 seconds ago      Up 52 seconds       0.0.0.0:80->80/tcp     nginx-golang_frontend_1
56f929c240a0        nginx-golang_backend    "/usr/local/bin/back…"   53 seconds ago      Up 53 seconds                              nginx-golang_backend_1
```

After the application starts, navigate to `http://localhost:80` in your web browser or run:

```.term1
curl localhost:80
```
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

[Golang Application](/){:data-term=".term1"}{:data-port="80"}

Stop and remove the containers

```.term1
docker-compose down
```
Results:

```
$ docker-compose down
Stopping nginxgolang_frontend_1 ... done
Stopping nginxgolang_backend_1  ... done
Removing nginxgolang_frontend_1 ... done
Removing nginxgolang_backend_1  ... done
Removing network nginxgolang_default
```