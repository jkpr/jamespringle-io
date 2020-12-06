+++
title = "Getting Redis up and running with Docker"
date = "2020-12-06T15:47:30-06:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["redis", "docker"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

Once [Docker][1] is installed on your machine, you have easy access to a lot of software. 
One benefit is uniform patterns of use: first, run a `docker pull ...` command to get an image, and then a `docker run ...` command to start that software. 
Of course, there is a lot more nuance if you want it, but this is the standard approach to start from.

Today, I will describe how to get the database software [Redis][2] running. 
Redis is a useful gadget to have in the toolbox, especially for web apps.

[1]: https://www.docker.com/
[2]: https://redis.io/

# Getting the image

The first step is to get the Redis image on your machine. 
Docker makes containers from images to run that software. On your command line, run

```bash
docker pull redis
```

to get the [official Redis image from Docker Hub][3].

[3]: https://hub.docker.com/_/redis

# Starting a container

In development situations, I often only need a temporary Redis instance. 
I also need it accessible from the code running on my machine. 
That means that I need a port exposed from Docker. In order to get a single-use instance of Redis with an exposed port, run

```bash
docker run -it --rm --name my-redis -p 6379:6379 redis
```

This names the container `my-redis`. Giving a container a name is optional.
After starting Redis, programs can connect at IP address `127.0.0.1:6379`. 
Note that we did not pass `-d` to the `docker run ...` command, so the container is not detached to run in the background.

# Checking on a container

With Redis running in a container, sometimes it is necessary to start the `redis-cli` in order to run manual queries on the database. 
In a new terminal, check the status of all running containers with

```bash
docker ps
```

Then, to connect to the running Redis instance, run

```bash
docker exec -it my-redis redis-cli
```

Where `my-redis` is the name or the ID of the container.

# Conclusion

You now have Redis up and running. You also know how to connect to a running container. 
From here, the development possibilities are endless!
