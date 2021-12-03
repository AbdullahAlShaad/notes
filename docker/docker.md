# Docker Notes

**Namespace** is introduced to achieve isolation. Process of different namespace cannot interact with each 
other or can not see each other. Namespace contains processes.

**Namespace** : Controls what you can see

**Cgroups** : Controls what resources you can use

- Each subsystem has a different hierarchy. Separate Trees for memory, CPU and IO
- Each process is in a node in each hierarchy
- Each node == Group of processes.
- Memory Cgroup keeps track of memory used
- Cpuset-> Pin groups of process to one CPU
- Each process is in one namespace of each type
- One process can not see other namespaces. It can only see its own namespace.

**Docker Image** : Docker image is executable of code along with dependencies defined in a Dockerfile.
When we execute Dockerfile, it creates a docker image.

**Container** : Container contains a docker image in the runtime. Containers are isolated from one another
Containers are built on mainly two things -> Namespace and Cgroups

**Docker vs Virtual Machine** :
- Docker runs on OS and Containers run on Docker. (Hardware -> OS -> Docker -> N Containers)
- In Virtual Machine , Hypervisor works between OS and Hardware, Multiple OS runs on Hypervisor. (Hardware -> Hypervisor -> N OS ) (OS -> Application)
- Docker has less isolation and more resource sharing than Virtual Machine.
- Docker Container has virtual file system and network to ensure isolation.
- Divide a machine for different customers is more suitable for using Virtual Machine. When I own the whole machine ,
I just need to run different service Docker is the best solution. Cause Docker is more lightweight.

We can combine Docker and Virtual Machine according to our need

**Docker Port Binding** :

When we run a container we get a completely different environment for the application.
The application have different sets of network configuration. 
If the application listens to port 80, its container's port 80 not host machines port 80.
So we can not access the port from local host machine. There are two ways to access that port :

1. Each container is given an IP address, we can use that IP address and find that port
2. We can map a port of Local host to the container port application is listening .

For example , we can map host port 8080 to port 80 of container. We can do this when running the container. 
After that, if we ping on host port 8080 , the container can listen

_Environment variable are set outside of program and used in program so that we can change
different behaviour of application without changing the code_


**Docker Network :**

Docker have 3 internal network :
- Bridge(Internal Docker Network) 
- None(Connect to None) and 
- Host(Host machine network)

Bridge Network is created by docker. Internal Network of the containers. Each container get an IP

We could create our own isolated network with given subnet and connect specific containers to that network.

Containers can connect with each other using each other name under the same network

**Docker Storage :**

- Docker have layered architecture of storage. Base image is the first layer. 
Each instruction in the docker file  creates a new layer.
- Each layer only stores changes from previous layer
- Docker caches the layer and re-use them to build other images
- Building container is just like adding another layer in storage level. 
  This added layer is container layer, and it stays as long as the container runs.
- All the layers except container layer is read only
- The files are copied in container layers, when we modify something we only modify a copy of that file
in container layer. Image layers are read only. This is why data is not persistent by default
- To persist data of container layer we use Volumes. Volume is created in host machine(by docker or manually)
and mapped to desired path of container file system

  
<h1>Docker Commands</h1>
<<<<<<< HEAD

=======
>>>>>>> d1764a860bb93721bfae5cf03a4288b8197f75eb
[src](https://github.com/rakibulhossain/Docker/blob/master/dockerStuff/dockerCommand.md)


<h2>Build an image</h2>

`docker build -t <image name:version> -f <dockerfile path> .`
<h2>Run an image</h2>

`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`
- frequently used options:
    - -it (interactive)
    - -d (detached in the background)
    - --name (container name)


<h2>Running Commands in the container using exec</h2>

`docker exec -it <container-name> /bin/sh`


<h2>Running Commands in the container using run</h2>

`docker run -it <image-name> /bin/sh`

<h2>Pull an image</h2>

`docker pull <image-name>:version`

<h2>Push an image</h2>

`docker push <image:name>:version`

<h2>Login</h2>

`docker login`

<h2>Logs</h2>

`docker logs [OPTIONS] CONTAINER`

<h2>Delete an image</h2>

`docker image rm <image-name>:version`
- use -f for force remove

<h2>Delete a container</h2>

`docker rm [OPTIONS] CONTAINER [CONTAINER...]`
- use -f for force remove


<h2>Start a container</h2>

`docker start [OPTIONS] CONTAINER [CONTAINER...]`

<h2>Stop a container</h2>

`docker stop [OPTIONS] CONTAINER [CONTAINER...]`

<h2>See the running container list</h2>

`docker ps`
- use --all for all types of container

<h2>Inspect an image</h2>

`docker image inspect <image:name>:version`

<h2>Mount a path in the container</h2>

`docker run -t -i -v <host_dir>:<container_dir>  ubuntu /bin/bash`


