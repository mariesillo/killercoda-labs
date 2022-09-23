---
layout: post
title:  "Cowsay"
date:   2018-07-02
tags: [developer,operations]
categories: other
---
## Prerequisites

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor. Prior experience in developing web applications will be helpful but is not required. As you proceed further along the tutorial, we'll make use of [Docker Cloud](https://cloud.docker.com/).

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

## Cowsay

Run a new Debian container in interactive mode, with name and hostname "cowsay" and start a bash session.

```.term1
 docker run -it --name cowsay --hostname cowsay debian bash
```

Notice the prompt has changed. This means you are now inside your docker container and all changes you make here will not impact the main server.
So in the next step, we will install cowsay and fortune packages in the container without modifying the server where it is running.

```.term1
apt-get update && apt-get install -y cowsay fortune
```

Now that we have modified the container installing the package we need, we can use the command below to genereta a random phrase and pipe it into the cowsay.

```.term1
/usr/games/fortune | /usr/games/cowsay
```

Then we run the below command to exit the container.

```.term1
exit
```

Once we have exited the container, the bash process we were on was terminated, you can verify the container status running:

```bash
docker ps -a
```

Note the container has stopped running. But we can also save the container with the changes made to run it again later.

The following command will save the changes made in the cowsay container as image with the name `test/cowsayimage`.

```.term1
docker commit cowsay test/cowsayimage
```

After saving it, we can run now our new image and call the command `test/cowsayimage /usr/games/cowsay "Moo"` to generate the cow

```.term1
docker run test/cowsayimage /usr/games/cowsay "Moo"
```

In the next step, we are going to create the same image but based on a Dockerfile.

This will help us create a new docker image automatically. But first, let's create a separate directory and create the Dockerfile in it.

```.term1
mkdir cowsay &&
cd cowsay &&
touch Dockerfile
```

Run the next command to add the content required in the Dockerfile.

```.term1
echo "
FROM debian

RUN apt-get update && apt-get install -y cowsay fortune" > Dockerfile
```

Please notice the two keywords in the Dockerfile: `FROM` and `RUN`.

- The `FROM` keyword helps us define the base image that we are going to use to add our changes.

- The `RUN` keyboard is used to place the commands that we need to run in our base image.

Once we have our Dockerfile with the instructions to build our cowsay image, let's use the `docker build` command:

```.term1
docker build -t test/cowsay-dockerfile .
```

- `"-t"` flag indicates the tag to assign to this new created image.
- `"."` is defining the context, here we are indicating docker to start the build in our current folder.

Now that we have an image with cowsay package installed previously, we can use the below command to run `cowsay "Moo"`

```.term1
docker run test/cowsay-dockerfile /usr/games/cowsay "Moo"
```

But what If we would like to define the command to run in the conntainer previously without the need to add it in the command line?

We can add an entry point, where we can call some actions from the start. In the next few steps we will add the keyboard ENTRYPOINT with a
shell script containing our actions to run.

Create entrypoint.sh file which will check if any arguments are received to show as text in the cow, if not, it will use fortune to generate a random text.

```.term1
echo '#!/bin/bash
if [ $# -eq 0 ]; then
    /usr/games/fortune | /usr/games/cowsay
  else
    /usr/games/cowsay "$@"
fi' > entrypoint.sh
```

Add execution permissions to the shell script

```.term1
chmod +x entrypoint.sh
```

Please notice that what we add to the entrypoint is executed inside the container, so we also need to copy the script to the container first, this is done with the COPY statement.

Once copied we can add the ENTRYPOINT statement and run the `docker build` command.

```.term1
echo 'FROM debian
RUN apt-get update && apt-get install -y cowsay fortune
COPY entrypoint.sh /
ENTRYPOINT ["/entrypoint.sh"]' > Dockerfile
```

```.term1
docker build -t test/cowsay-dockerfile-entrypoint .
```

And run the cowsay image with and without parameters.

```.term1
docker run -t test/cowsay-dockerfile-entrypoint
```

```.term1
docker run -t test/cowsay-dockerfile-entrypoint Hello
```

Finally, try the below cows.

- calvin
- ghostbusters
- tux
- vader
- vader-koala

Example:

```.term1
docker run -t test/cowsay-dockerfile-entrypoint -f tux Hello from container
```

All cows are loccated in `/usr/share/cowsay/cows`
