---
layout: post
title:  "Swarm Visualizer"
date:   2021-04-27
tags: [developer,operations,networking]
categories: other
terms: 2
---

### Pre-requirements:

In order to avoid any issues pulling images we strongly suggest to login into docker first:

*Please user your own docker username / passwd

Click bellow command to login into Terminal 1
```.term1
docker login
```
Click bellow command to login into Terminal 2
```.term2
docker login
```

## Init your swarm

```.term1
docker swarm init --advertise-addr $(hostname -i)
```

Copy the join command (*watch out for newlines*) output and paste it in the other terminal.


## Show members of swarm

Type the below command in the first terminal:

```.term1
docker node ls
```

That last line will show you a list of all the nodes, something like this:

```
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGE
R STATUS
kytp4gq5mrvmdbb0qpifdxeiv *  node1     Ready   Active        Leader
lz1j4d6290j8lityk4w0cxls5    node2     Ready   Active
```

If you try to execute an administrative command in a non-leader node `worker`, you'll get an error. Try it here:

```.term2
docker node ls
```

## Creating services


The next step is to create a service and list out the services. This creates a single service called `web` that runs the latest nginx, type the below commands in the first terminal:


```.term1
docker service create -p 80:80 --name web nginx:latest
```

You can check that nginx is running by executing the following command:

```.term1
docker service ls
```

We are also creating a visualizer to display Docker services running on a Docker Swarm in a diagram:

```.term1
docker run -it -d -p 5000:8080 -v /var/run/docker.sock:/var/run/docker.sock dockersamples/visualizer
```

[Visualizer Console](/){:data-term=".term1"}{:data-port="5000"}


## Scaling up

We will be performing these actions in the first terminal. Next let's inspect the service:

```.term1
docker service inspect web
```

That's lots of info! Now, let's scale the service:

```.term1
docker service scale web=8
```

Docker has spread the 8 services evenly over all of the nodes

```.term1
docker service ps web
```

[Visualizer Console](/){:data-term=".term1"}{:data-port="5000"}


## Updating nodes

You can also drain a particular node, that is remove all services from that node. The services will automatically be rescheduled on other nodes.

```.term1
docker node update --availability drain node2
```
```.term1
docker service ps web
```

You can check out the nodes and see that `node2` is still active but drained.

```.term1
docker node ls
```

[Visualizer Console](/){:data-term=".term1"}{:data-port="5000"}


## Scaling down

You can also scale down the service

```.term1
docker service scale web=4
```

Lets check our service status

```.term1
docker service ps web
```

[Visualizer Console](/){:data-term=".term1"}{:data-port="5000"}


Now bring `node2` back online and show it's new availability

```.term1
docker node update --availability active node2
```
```.term1
docker node inspect node2 --pretty
```

Let's create another service called `web1` which its going to be scheduled depending on availability into the swarm cluster.


```.term1
docker service create -p 81:80 --name web1 nginx:latest
```

You can check that nginx is running by executing the following command:

```.term1
docker service ls
```

```
$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
4qempzthmt1g        web                 replicated          4/4                 nginx:latest        *:80->80/tcp
qy3c3vm76lh7        web1                replicated          1/1                 nginx:latest        *:81->80/tcp
```

[Visualizer Console](/){:data-term=".term1"}{:data-port="5000"}
