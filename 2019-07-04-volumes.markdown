---
layout: post
title:  "Docker Volumes"
date:   2019-07-04
tags: [Linux,operations]
categories: other
---

## Prerequisites

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor.

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

## Docker data persistency

Let's start by running a shell session in an Alpine container with interactive mode.

```.term1
docker run -it --name container1 alpine sh
```

After the image is downloaded and run, you will see the prompt change.

This means we are now inside the shell session in our alpine container.

```.term1
ls -l 
```

Note that all command run in until we exit the shell session are executed inside the alpine container, so the files we see are not the ones in the server that is running the docker container.

Now let's save this list of files in a new text file called testfile.txt

```.term1
ls -l > testfile.txt
```

We can now check that the result of the ls -l command has been saved on testfile.txt

```.term1
cat testfile.txt
```

Then we can exit the shell session.

```.term1
exit
```

With the below command we can list the currently running containers in our system.

```.term1
docker ps
```

Please notice that our "container1" is not listed here. This is because ones the container has finished the shell proccess we told it to run, after this there is no other purpose for it so it stops.

```.term1
docker ps -a
```

`docker ps -a` shows all containers that have been created no matter their status. Here you must be able to see "container1" listed with exited status.

Now that we have found our container, let's start it again.

```.term1
docker start container1 
```

With our "container1" running (an sh process that we defined when we created it with the run command), we can send the command to list the file we have created before.

```.term1
docker exec container1 ls -l testfile.txt
```

You should see the file listed there. So it exists even after the container has stopped and started again.

But what happens if the container is recreated wither because a system issue or a human error? Will the file persist somewhere inn the system?

In order to test it we are going to stop and then delete the container running the below commands.

```.term1
docker stop container1 && docker rm container1
```

Now we can confirm the container has been deleted successfully by running `docker ps -a` again. The container shouldn't be listed even in exited state since it does no longer exists.

Let's run a new container with the same name again, this time using the flag `-d`for detached mode.

```.term1
docker run -itd --name container1 alpine sh
```

Now we have our new "container1" running and we can confirm it running `docker ps`.

So let's check if our test file is still there.

```.term1
docker exec container1 ls -l testfile.txt
```

The file is not there but, why?

By default all files created inside a container are stored on a writable container layer. This means that the data doesn’t persist when that container no longer exists, and it can be difficult to get the data out of the container if another process needs it.

Docker has an option for containers to store files in the host machine, so that the files are persisted even after the container gets deleted.

## Mapping path from hosts

In order to map a folder in the host machine and share it with the container we use the flag `-v`

The parameters required by the `-v` flag are the folder from the host machine, followed by `:` and the path inside the container where it is going to be mapped.

```.term1
docker run -it --name container2 -v /data:/home/user1 alpine sh
```

Then we create a new test file inside the mapped folder of the container and exit from it.

```.term1
echo "volume from path - test file" > /home/user1/testfile2.txt
```

```.term1
exit
```

Stop and delete the container using the command below.

```.term1
docker rm $( docker stop container2 )
```

You can confirm it was successsfully deleted again with the `docker ps -a` command.

Now navigate to the /data folder in the host machine and list the content, the file created must be there.

```.term1
cd /data
```

```.term1
ls -l
```

Docker volume mapping also let us reuse the folder we mapped before in a new container

```.term1
docker run -it --name container3 -v /data:/home/user1 alpine sh
```

Create a new test file (testfile3.txt) and exit the container.

```.term1
echo "new test file" > /home/user1/testfile3.txt && exit
```

Confirm the container has stopped

```.term1
docker ps
```

Even when "container3" has not been removed (just stopped), we can use the same volume in another container.

```.term1
docker run -it --name container4 -v /data:/home/user2 alpine sh
```

Finally confirm you can acccess the testfile3.txt inside the new container and the content is still there.

```.term1
ls -l /home/user2/testfile3.txt
```

```.term1
cat /home/user2/testfile3.txt
```

To learn more about storage data in docker containers, go to [Manage Data in Docker](https://docs.docker.com/storage/).
