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



