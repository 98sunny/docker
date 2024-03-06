## Docker
helps create an environement which is as close to a production environment.


## Why containers?
Create dev environment is as easy as ---> "run docker compose up"

## What is a Container?
A docker container image is a standalone, lightweight, executable (SLE) bundle of software which are required to run an application without depending on anything else.

## Open Container Initiative (OCI)
An Open governance structure for express purpose of creating open industry standards around container formats and runtime.
- Runtime Specification
- Image Specification
- Distribution Specification

##  Comparision between bare metal, VM and container
## Bare Metal
![Bare metal](./images/bare-metal.jpg)
1. Hellish dependency conflicts
2. Low utilization efficiency
3. Large blast radius
4. Slow start up & shut down speed (minutes)
5. Very slow provisioning & decommissioning (hours to days)

![VM](./images/vm.jpg)
1. No dependency conflicts
2. Better utilization efficiency
3. Small blast radius
4. Faster startup and shutdown (minutes)
5. Faster provisioning & decommissioning (minutes)


![Container](./images/container.jpg)
1. No dependency conflicts
2. Even better utilization efficiency
3. Small blast radius
4. Even faster startup and shutdown (seconds)
5. Even faster provisioning & decommissioning (seconds)
6. Lightweight enough to use in development!


## Technology Overview
3 core technologies of Docker:
- Namespaces
- Control Group
- Union Filesystem

### Namespace
A namespace wraps global system resource in an abstraction that makes it appear to the processes within a namespace that they have their own isolated instance of thr global resource.

Changes to the global resource are visible to other processes that are member of the namespace, but are invisible to other processes.

Example: PIDs, basically when we see what processes are running on the container, we will not be able to see what all is running on the host which is hosting the container. By this, we are securing the processes and info about anything running on the host.

### Cgroups
A linux kernel feature which allow processes to be organized into heirarchical groups whose usage of various types of resources can be limited and monitored.

Basically tells what process uses how much is the consumption.
This is important so that we can isolate different processes and each of our different applications.

Using cgroups, we can configure which process should be able to access hosts resources

Example: 
Application A usee 30% of CPU cycles.
Application A uses up to 50MB memory.

Application B uses 40% of CPU cycles.
Application B use up to 100MB memory.


### Union Mount FileSystems(overlayfs)
Allows files and directories of separate file systems, known as branches

## Docker architecture
![Docker architecture](./images/docker-architecture.jpg)

## Running first container
docker run docker/whalesay cowsay "Hey Hiring Team, Sunny is a good DevOps engineer who likes new technologies"

## Getting postgres running fr our project
docker run --env POSTGRES_PASSWORD=foobarbaz --publish 5432:5432 postgres:15.1-alpine

We are passing the password for Postgres in POSTGRES_PASSWORD as an environment variable.
We are also telling that there is a port called 5432 with which we can access the Postgres instance which will running on the isolated network from the host and the second 5432 is the port on the container.
And finally, we mention which version of POstgres we want.


## Understanding container Data with Container
Default behavior: All data created or modified in container will be lost if container is stopped/deleted. 
## Solution
If we want some data to be present everytime a container image is run, it should be built into the image itself.
![Container FileSystem](./images/container-filesystem.jpg)

If we want data generated or modified by container to be **persistant**, we should use a volume to store that outside of the container file system. 
OR, we could use **DOCKER VOLUME**.

![Persistant Volume](./images/volumes.jpg)

# Some practical
Creating a container from the Ubuntu image
**docker run --interactive --tty --rm ubuntu:22.04** 

--interactive and --tty helps us to get the shell to interact with the Ubuntu container
-rm removes the container from the host once we exit the shell and does not keep the image of the container which we created.

Updating the OS
```apt update```

Installing the ping utility

To get ping utility
```apt install iputils-ping```

Test
```ping google.com -c 1```


Now, running the same command without rm and giving the container name this time:
**docker run --interactive --tty --name sunny-ubuntu-container ubuntu:22.04**
Updating the OS
**apt update**


**Start the container again**
```docker start sunny-ubuntu-container```

**Attach command to go into the container shell**
```docker attach sunny-ubuntu-container```

Problem: everytime we build the container using the container image, we have to update it and install the iputils command. This is not efficient. Hence, we build a container image of our own.

**Build a container image with Ubuntu image as base image and ping installed**

```
docker build --tag sunny-ubuntu-image -<<EOF
FROM ubuntu:22.04
RUN apt update && apt install iputils-ping --yes
EOF
```

This way we are persisting the data and the changes whcih we are making in the container image.
The FROM... RUN... stuff is part of what is called a Dockerfile that is used to specify how to build a container image.


**Running the new container**
``` docker run -it --rm sunny-ubuntu-image ```
Now, whatever files we create inside this will not perish as it in not on persistant volume.



### Volume Mounts
We can use volumes and mounts to safely persist the data. 
docker run -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04

``` 
## creating a volume
docker volume create my-volume 
```

### Locating a volume
Fro example, you don't know where the volume was set
```
docker volume inspect my-volume

[
    {
        "CreatedAt": "2024-03-05T13:25:47Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/my-volume/_data",
        "Name": "my-volume",
        "Options": null,
        "Scope": "local"
    }
]
```


## Container data and Docker Volume
```
# Create a container and mount the volume into the container filesystem
docker run  -it --rm --mount source=my-volume,destination='/c/Users/sunny/IdeaProjects/docker' ubuntu:22.04
# There is a similar (but shorter) syntax using -v which accomplishes the same
docker run  -it --rm -v my-volume:/my-data ubuntu:22.04
```

# Create a container and mount the volume into the container filesystem
docker run  -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04

docker run  -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04
# There is a similar (but shorter) syntax using -v which accomplishes the same
```
docker run  -it --rm -v my-volume:/my-data ubuntu:22.04

```

#  Bind Mounts->Creating a container and mounting the volume from local disk
```
# Create a container that mounts a directory from the host filesystem into the container
docker run  -it --rm --mount type=bind,source="${PWD}"/my-data,destination=/my-data ubuntu:22.04
# Again, there is a similar (but shorter) syntax using -v which accomplishes the same
docker run  -it --rm -v ${PWD}/my-data:/my-data ubuntu:22.04

echo "Hello from the container written by Sunny from inside container!" > /my-data/hello.txt

# You should also be able to see the hello.txt file on your host system
cat my-data/hello.txt
exit
```






