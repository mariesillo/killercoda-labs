### Pre-requirements:

In order to avoid any issues pulling images we strongly suggest to login into docker first:

*Please user your own docker username / passwd

Click bellow command to login
```
docker login
```{{exec}}

## Init your swarm

```
docker swarm init --advertise-addr $(hostname -i)
```{{exec}}

## Show members of swarm

In order to visualize the nodes in the cluster we will execute following command:

```
docker node ls
```{{exec}}

That last line will show you a list of all the nodes, something like this:

```
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
a26fslcfhxmnbw6grmfpgnpdv *   ubuntu     Ready     Active         Leader           20.10.12
```

## Creating services


The next step is to create a service and list out the services. This creates a single service called `web` that runs the latest nginx, type the below commands in the first terminal:


```
docker service create -p 80:80 --name web nginx:latest
```{{exec}}

You can check that nginx is running by executing the following command:

```
docker service ls
```{{exec}}

We are also creating a visualizer to display Docker services running on a Docker Swarm in a diagram:

```
docker run -it -d -p 5000:8080 -v /var/run/docker.sock:/var/run/docker.sock dockersamples/visualizer
```{{exec}}

[Visualizer Console]({{TRAFFIC_HOST1_5000}})


## Scaling up

We will be performing these actions in the first terminal. Next let's inspect the service:

```
docker service inspect web
```{{exec}}

That's lots of info! Now, let's scale the service:

```
docker service scale web=8
```{{exec}}

Docker has spread the 8 services evenly over all of the nodes

```
docker service ps web
```{{exec}}

[Visualizer Console]({{TRAFFIC_HOST1_5000}})

## Scaling down

You can also scale down the service

```
docker service scale web=4
```{{exec}}

Lets check our service status

```
docker service ps web
```{{exec}}

[Visualizer Console]({{TRAFFIC_HOST1_5000}})


Let's create another service called `web1` which its going to be scheduled depending on availability into the swarm cluster.


```
docker service create -p 81:80 --name web1 nginx:latest
```{{exec}}

You can check that nginx is running by executing the following command:

```
docker service ls
```{{exec}}

```
$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
4qempzthmt1g        web                 replicated          4/4                 nginx:latest        *:80->80/tcp
qy3c3vm76lh7        web1                replicated          1/1                 nginx:latest        *:81->80/tcp
```

[Visualizer Console]({{TRAFFIC_HOST1_5000}})
