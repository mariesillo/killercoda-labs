---
layout: post
title:  "Docker Ports"
date:   2019-07-04
tags: [Linux,operations]
categories: other
---

## Prerequisites

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor. Prior experience with basic webserver configuration and networking will be helpful but is not required.

> Note: If you get an error like:
>  
> ```bash
> docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit.
> See 'docker run --help'.
> ```
>  
> You will have to login with your docker ID before starting this tutorial.
Run the command below and then enter your username and password.
>
> ```.term1
> docker login
> ```
>
> If you don't have a docker hub ID yet, go to [Docker hub signup](https://hub.docker.com/signup) page.

## Port configuration for external connectivity

In this step we’ll start a new NGINX container and map port 8080 on the Docker host to port 80 inside of the container. This means that traffic that hits the Docker host on port 8080 will be passed on to port 80 inside the container.

```.term1
docker run -itd --name nginx1 -p 8080:80 nginx
```

Now we have our container running onn port 8080, go to [Nginx Webserver](/){:data-term=".term1"}{:data-port="8080"} to verify the result.

We can use multiple port numbers of the loccal machine to export our conntainer ports, but there must not be more than one container or service usingn a port. Notice the error running the next command:

```.term1
docker run -itd --name nginx2 -p 8080:80 nginx
```

Docker deamon response it is unable to bind the expecified port. Nevertheless, we can use any other port that is not in use

```.term1
docker run -itd --name nginx3 -v /myapp:/usr/share/nginx/html -p 9090:80 nginx
```

Go to [Nginx Webserver](/){:data-term=".term1"}{:data-port="9090"} to check the result, there should be a "forbidden" message, but why?

Note we have mapped a folder on the local machine to the nginx2 container, pointing the /usr/share/nginx/html directory. This folder is where nginx looks for the html resources to expose, you can check in more detail Nginx configuration in its [docker hub page](https://hub.docker.com/_/nginx).

Since we haven't added any html to the folder we defined, Nginx returns a "forrbidden" ressults to the user. Let's add a basic html page.

Go to the folder we have previously mapped to the container and add a basic html text on the index.html file.

```.term1
cd /myapp
```

```.term1
echo '<html>
<header><title></title></header>
<body>
<h1>Hello Everyone!!!</h1>
</body>
</html>' > index.html
```

You should now be able to see your "__Hello Everyone!!!__" message exposed in the [Nginx Webserver](/){:data-term=".term1"}{:data-port="9090"}

To learn more about container networking go to the [docker hub page](https://docs.docker.com/config/containers/container-networking/)
