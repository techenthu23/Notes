---
layout: page
title: Docker
---

- [Docker](#docker)
  - [WHAT IS A NAMESPACE?](#what-is-a-namespace)
  - [WHAT ARE CGROUPS?](#what-are-cgroups)
- [Why Docker-compose?](#why-docker-compose)
  - [Prerequisites](#prerequisites)
  - [Docker-compose keys description](#docker-compose-keys-description)
    - [version](#version)
    - [services](#services)
    - [image](#image)
    - [volumes](#volumes)
    - [environment](#environment)
    - [env_file](#env_file)
    - [networks](#networks)
    - [build](#build)
    - [ports](#ports)
    - [depends_on](#depends_on)
    - [container_name](#container_name)
    - [stdin_open: and tty](#stdin_open-and-tty)
  - [Starting services using docker-compose](#starting-services-using-docker-compose)
  
# Docker

**Docker** is a set of platform as a service ( **PaaS** ) products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels. Because all of the containers share the services of a single operating system kernel, they use fewer resources than virtual machines.

The service has both free and premium tiers. The software that hosts the containers is called **Docker Engine**. It was first started in 2013 and is developed by Docker, Inc.

Docker can package an application and its dependencies in a virtual container that can run on any Linux, Windows, or macOS computer. This enables the application to run in a variety of locations, such as on-premises, in a public cloud, and/or in a private cloud.

When running on Linux, Docker uses the resource isolation features of the Linux kernel (such as cgroups and kernel namespaces) and a union-capable file system (such as OverlayFS) to allow containers to run within a single Linux instance, avoiding the overhead of starting and maintaining virtual machines.

Namespaces (PID, network, mount, etc.) , along with other technologies like **cgroups** , Linux security constraints (Unix permissions, capabilities, SELinux, AppArmor, seccomp, etc.) and more, form the foundation of containerization.

The Linux kernel's support for namespaces mostly isolates an application's view of the operating environment, including process trees, network, user IDs and mounted file systems, while the kernel's cgroups provide resource limiting for memory and CPU. Since version 0.9, Docker includes its own component (called "libcontainer") to directly use virtualization facilities provided by the Linux kernel, in addition to using abstracted virtualization interfaces via libvirt, LXC and systemd-nspawn.

Docker implements a high-level API to provide lightweight containers that run processes in isolation.

Because Docker containers are lightweight, a single server or virtual machine can run several containers simultaneously.

A 2018 analysis found that a typical Docker use case involves running eight containers per host, and that a quarter of analyzed organizations run 18 or more per host.

### WHAT IS A NAMESPACE?

You may consider a namespace as a global system wrapper. It means it takes a global system resource like a mount point and it provides a wrapper around it that makes it look to the process living in that namespace like it has its own isolated instance of that resource. Namespaces allow the partitioning of kernel resources ensuring that one set of processes sees only the resources allocated to it while another set of processes sees only the resources allocated to it.

namespaces and cgroups are referenced interchangeably but this is not accurate. simply put, namespaces limit what resources a process or a set of processes can see whereas cgroups limit what resources a process or a set of processes can use.

here are six different types of namespaces described below:

- **User namespace** :

This is a key security feature as each namespace can be given its own distinct set of user ids and group ids. It allows a process to have a PID of 1 inside a container and a PID of 2000 inside the host system. In fact, user namespaces can be nested up to 32 times. With the implementation of user namespaces in containers, an attacker could gain root access to a container but since that would be limited to the particular container, they would not be able to do anything on the host operating system.

- **Interprocess communication (IPC) namespace:**

This isolates system resources from a process while giving processes created in an IPC namespace visibility to each other for inter-process communication. This offers a way for multiple processes to exchange data.

- **UNIX Time-Sharing (UTS) namespace:**

The UTS namespace allows a single system to have a different host and domain name for different processes. This was implemented to allow the isolation of a different hostname for each container. This allows containers to have their own names so that applications could use container hostnames as default identifiers when communicating with containers as well as allow users to get information about containers using traditional commands like uname or hostname.

- **Mount namespace:**

The name of the mount namespace is self-explanatory. This controls the mount points visible to containers. Traditionally mounting or unmounting a file system would change the global system environment. However, with the use of the mount namespace, we can now provide isolated lists of mount points for each container. The process of creating a mount namespace is similar to that of creating a chrooted environment.

- **PID namespace:**

The PID namespace allows for the isolation of process id numbers. Every time you boot up a Linux system, it will start with just one process with the PID of 1 and that process is the root of the process tree. The PID namespace allows us to create a new process tree for each container. This ability helps satisfy the init system’s need to be PID 1 in that container. This also allows the functionality to suspend the process and move the container to a brand new host with the process still in place so that when we bring the container back up we don’t have to worry about PID number conflicts.

- **Network namespace:**

This allows containers to have their own copy of the network stack. This allows every container to have its own routing table, its own firewall rules, and its own network devices

For containers, a namespace is what defines the boundaries of a process' "awareness" of what else is running around it.

|                                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lsns                                  | To list existing name space. lsns is part of util-linux package                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ls /proc/*/ns                         | Each process running on your Linux machine is enumerated with a process ID (PID). Each PID is assigned a namespace. PIDs in the same namespace can have access to one another because they are programmed to operate within a given namespace. PIDs in different namespaces are unable to interact with one another by default because they are running in a different context, or namespace. This is why a process running in a "container" under one namespace cannot access information outside its container or information running inside a different container. |
| unshare --fork --pid --mount-proc zsh | **unshare** command runs a program in a namespace *unshared* from its parent process.<br>In this case the PID of new forked process will be 1 , which is usually reserved on the Linux host for boot initialization program. Zsh sees itself as PID 1 only because its scope is confined to (or *contained* within) its namespace but when checked on Linux host via other termianl it actually running as some high-numbered PID.                                                                                                                                    |

### WHAT ARE CGROUPS?

Cgroups or control groups were originally released by google as process containers. However, to avoid any issue with the word containers, the name was changed. Cgroups isolate a process’s ability to have access to a system resource. A process within a cgroup does not have to behave the same way as traditional processes as each subsystem may have its own process hierarchies which are independent of one another. This means that one process can live in several trees. There are several different subsystems that are supported by the Linux kernel and we will now describe some of the more important ones.

- blkio:

This allows you to limit and measure the amount of I/Os for each set of processes. It allows you to throttle limits for each of the groups.

- cpu:

The cpu subsystem allows you to monitor cpu usage for a group of processes allowing you to set weights and keep track of the usage per cpu.

- cpuacct:

This generates automatic reports on CPU resources used by tasks in a cgroup.

- cpuset:

The cpuset subsystem allows you to ping groups of processes to one CPU or to groups of a process allowing you to dedicate CPUs to a particular task.

- device:

This allows or denies access to devices by tasks in a cgroup. It allows you to set permissions as to which processes or containers could read or write to a device.

- freezer:

The freezer subsystem suspends or resumes tasks in a cgroup. It can be used to send a sigstop signal to a whole container.

- memory:

It sets limits of the amount of memory that can be used by tasks in a cgroup and generates automatic reports on memory resource usage. The memory subsystem allows you to keep track of memory usage down to the memory page level.

- net\_cls:

The net\_cls subsystem enables us to tag network packets with a classid that allows the identification of packets originating from a particular cgroup task.

- net\_prio:

The net\_prio subsystem provides a way to set the priority of network packets dynamically.

# What are a docker image and docker container?

As per the official website, a Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings.

And docker container is an instance of the docker image, docker container is a docker image brought to life.

Breakdown of docker commands syntax:

Every docker command can be broken down into 3 parts :

1. Keyword 'docker'
2. Main task (start, stop, build, etc).
3. Options (port, tty, interactive volumes, etc).
4. Reference to image or container with all the property flags.

Every docker command starts with the ‘docker’ keyword.

2nd part of the command represents the main task which we want to execute on a docker image or on a docker container (like run for running a container, build for building an image, etc).

3rd part includes the extra options like the port number mapping (-p ), interactive communication (-i), tty (-t), volumes (-v), etc. A flag with a single dash (-) is the shortcut for the full name flag like (-p for — port) and a double dash is used for the full name.

4th part is the reference to an image or a container (like the container-id, image-name, image-id, etc).

## Important Container commands

### Creating a container

```
docker create {image-id/image-name}
```

Creates a container from a docker image. It returns a container id, which can be used for starting that container.

### Starting a container

```
docker start {container-id}
```

Starts a container, but this command will not return any logs to the docker-CLI. To get the logs we need to add -a flag like

```

docker start -a {container-id}
```

### Running a container

```
docker run {image-id/image-name}
```

Pull and starts the container, it is a combination of docker create and docker start command and there is no need of adding any external flag to get the logs on docker CLI.

### Listing all running containers

```
docker container ls
```

Lists all the running containers. There is an alternative command to list all the running container

```
docker ps
```

Listing all container

```
docker ps --all 
docker ps -a
```

Lists all the container that includes running containers, stopped containers, and newly created containers.

### Inspecting container

```
docker inspect {container-id}
```

Returns all the information about the container like source, image used to create this container, restart count, platform, current state, etc.

### Checking logs

```
docker logs {container-id}
```

Prints the logs on the terminal. It can be used in the scenarios when you used docker start {container-id} command and forgot to add -a tag.

### Stopping container

```
docker stop {container-id}
```

Stops the running docker container gracefully. It takes its time to do the cleanup work and then stops the container, which is less than 10 seconds because if the cleanup work is taking more than 10 seconds then it runs the kill command.

### Killing container

```
docker kill {container-id}
```

Stops the container abruptly, instant stopping of the container.

### Removing container

```
docker rm {container-id}
```

Deletes a stopped container, We cannot remove a running container. First, we have to stop/kill the container then only we can delete or remove that container.

### Removing containers

```
docker container prune
```

You will get the prompt ‘Are you sure…’, If you want to override this prompt then you can use force flag (--force or -f)

### Removing containers using filters

Removes containers which are older than 12 hours

```
docker container prune --filter "until=12h"
```

### Removing all container except the given container

```
docker container prune --filter "label!=mycontainer"
```

Similarly, we can use other filters as well.

## Important image commands

### Building an image

The most basic command to build an image from a docker file. This represents the build context, we can use this dot only when we are executing the command in the same directory where the Dockerfile is present. This command will return the image id.

```
docker build .
```

But the build number is not that easy to remember, So we can add a tag to give a name to out build.

```
docker build -t {username}/my_build:{tag} .
```

Here username is the username of the docker hub account, my_build is the name of the image which we are building and the tag is the version of the build and dot is again present for the build context.
We can add more things like port, volume, etc in the build command.

### Pushing image to the registry

```
docker push {image-id/ image-name:{tag}}
```

Adds our image to the remote repository. So that we can pull it every time we need it, It's like committing our image to a remote repository.

### Listing all images

```
docker image ls
```

Lists all the images present in your system that includes both pulled images from docker hub repository and locally created images from docker files.

### Checking image history

```
docker image history {image-name/image-id}
```

Given all the details like when it was created, what is the size of the image, how many times the same image was created, etc

### Inspecting image

```
docker image inspect {image-id/image-name}
```

Returns all the information about the image, information like volume, created date, docker version, working directory, docker file details which are used to create this image, etc.

### Deleting or removing an image

```
docker image rm {image-id/ image-name}
```

Deletes/remove an image from the image list. So, if we are done using any image we can remove the unnecessary images using this command. Sometimes we get messages like:

```
Error response from daemon: conflict: unable to delete bf756fb1ae65 (must be forced) — image is being used by stopped container 85da219662dc
```

So in this case we can remove the dependent image first and then remove the current image (preferred option) or we can force delete this image using

```
docker image rm {image-id/ image-name} --force
```

### Removing all dangling images

```
docker image prune
```

Removes all the dangling images from the system.
To remove all the unused images with the dandling ones, use the same command with -a option (-a is for --attach).

```
docker image prune -a
```

### Removing images using filters

It is the same as removing containers using filters,
we can use a filter with --filter option.

```
docker image prune -a --filter "until=12h"
```

Removes/deletes all the dangling and unused images older than 12 hours.

## Miscellaneous commands

### Checking version

```
docker version
```

Gives the complete description of the docker version.

### Deleting all the unused containers and images

```
docker system prune
```

It first prompts that ‘Are you really want to continue’ and if you press Y then it will remove all the unused containers, images, and networks
By default, this command does not remove volumes. So to remove the volumes we have to pass extra options.

```
docker system prune --volumes
```

### Removing all unused volumes

```
docker volume prune
```

## Advance commands with options

### Running the container in the background

```
docker run -d {image-id/ image-name}
```

Here -d is used for detach, it will run this container in the background. It allows you to use the terminal for other commands.

### Adding port

```
docker run -p 4000:8080 {image-id/image-name}
```

Here -p is the short form for port and 4000:8080 means any request coming on port 4000 on the specified host will be redirected to 8080 port of the container.

### Connecting to STDIN and STDOUT

```
docker run -i -t -p 4000:8080 {image-id/image-name}
```

-i is short for interactive and is used for opening a connection to docker client (STDIN) -t is short for --tty, it allocates a pseudo-terminal that connects your terminal with docker client for interaction. (STDIN and STDOUT)
we can combine these two commands

```
docker run -it -p 4000:8080 {image-id/image-name}
```

### Auto deleting container

```
docker run -it -p 4000:8080 -rm {image-id/image-name}
```

Here the -rm is short for removal and it is used for automatically deleting the container when it stops.

### Executing another command on a running container

```
docker exec -it {container-id} {command}
```

This is used when we have to execute multiple commands like in redis, we have to start the redis server and redis-CLI to interact with the redis server, So, we can use exec command like

```
docker run redis
docker exec -it {container-id} redis-cli
```

Here the container id is the id of the Redis container which gets created from 1st command.

### Opening the terminal in any container

If we want to see which all programs or data are present in any container then we can open a shell terminal inside the container using the exec command.

```
docker exec -it {container-id} sh
```

# Why Docker-compose?

Let say we want to containerize an application having Node backend, React-js frontend, and MongoDB database. If we go by the simple approach then we need to first create the images of all the applications individually then run those images using the docker run command. It requires multiple long docker commands. Similarly, if we have to stop the application then we have to stop all the containers individually.
Docker-compose solves this issue. we can define the details of all the containers that we want to start in one YAML file and just execute one command to run the docker-compose file and that's it, the docker-compose file will take care of building the images, running the containers using those images, dependency between the containers, networking, and many more things.
NOTE: Docker-compose file is not a replacement of the Dockerfiles, they solve two different purposes.

## Prerequisites

1. Docker installation
The primary prerequisite is to have docker installed in your system. Execute the following command to check the docker version

```
docker --version
OR
docker -v
```

If you get the proper version means docker is already installed in your system otherwise follow the below link to install the docker in your system
<https://docs.docker.com/engine/install/>

2. Docker-compose installation
We need to have docker-compose installed in our system. It is installed with docker for mac and windows but for Linux, we have to install it separately. To check that if it is already installed, run the following command

```
docker-compose -v
OR
docker-compose --version
```

If it is not already installed then follow the below link to install it in your system
<https://docs.docker.com/compose/install/>

3. Basic docker knowledge
We don't need to be a docker expert to understand this article but you should have a basic understanding of docker terminology like container, image, volume, Dockerfile, etc.

4. Basic YAML knowledge
As docker-compose is totally based upon the YAML file So, it makes it very important to know how to read and write a basic YAML file.

Suggestion: I would like to suggest using VS code and install the Docker extension in it. It will add IntelliSense to the project, it will help you with proper suggestions of the docker-compose keys

## Creating docker-compose file

We can create a docker-compose file at the top level of the project, it is not mandatory but it makes it easy to access the Dockerfiles present in the project. We must follow the standard of the file name, it must have ‘docker-compose’ as name and ‘yaml’ or ‘yml’ as an extension.

## Docker-compose keys description

Now we will see the details of all the docker-compose keys that can be used to specify the details of all the containers that we want to run. As docker-compose is a YAML file and in YAML file indentation, case-sensitivity matters. So we have to take extra care of all these small details.

### version

Every docker-compose file starts with the version property. It specifies the version of docker-compose specifications we want to use. It affects the features we can use in our docker-compose file.

```
version: "3.8"
```

### services

It is the main key under that we define the details of all the containers that we want to run.

```
version: "3.8"
services:
  mongodb:
    ------------------
    ------------------
    ------------------
  node-app:
    ------------------
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
```

Here, MongoDB, node-app, and react-app are the names of the services, dotted lines are the placeholders for the details of the services that we will discuss next. Every service defines a single container. The name of the service is not equal to the name of the container.

### image

It defines the image of the container that we want to run, it can be a local image that we created from a Dockerfile or it can be an existing image present in any repository.

```
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    ------------------
    ------------------
  node-app:
    ------------------
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
```

If the image does not exist locally and we did not specify the repository then it will by default checks the central docker repository. We can specify the custom repository like

```
image: 'custom-repository/mongo'
```

### volumes

It is used to define all the volumes to be used in a container. We can specify all the volumes as a list under the volumes key. We can add named volume, anonymous volume as well as bind-mounts under the volumes key.

```
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    ------------------
  node-app:
    ------------------
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

Here ‘data’ is the named volume that is pointing to the ‘data/db’ folder of the container. It will persist all the data of ‘data/db’ folder on the local machine.
At the end of the docker-compose file, we have to specify all the named volumes we are using in all the containers. Currently, we are using only 1 named volume so, we specified it.

### environment

It is used to specify the environment variables to be used in the application running inside that specific container. There are two ways of specifying the environment variables

```
environment:
  MONGO_USERNAME: root
```
  
OR

```yaml
environment:
  - MONGO_USERNAME=root
```

Both will work, but I suggest using the first approach as it satisfies the YAML contract conditions

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    environment:
      MONGO_USERNAME: root
      MONGO_PASSWORD: root
  node-app:
    ------------------
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

### env_file

In the last point we specified all the environment variables inside the docker-compose file, this approach is ok when we have few environment variables but if we have many variables then we can move those variables inside an environment variable file and can refer to the path (relative path to the docker-compose file) of the file in the docker-compose file.
First, we will create a folder parallel to the docker-compose file (as shown in figure-1) then we will create a mongo.env file inside it (the name is up to you but the extension should be .env). We can create this file anywhere we want, but it's a good practice to create it parallel to the docker-compose file to improve the readability of the application and configuration.

we can take our environment variables and can place them in the mongo.env file as shown

```yaml
MONGO_USERNAME=root
MONGO_PASSWORD=root
```

Refer to the address (relative to the docker-compose file) of the environmental file in the docker-compose file.

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
  node-app:
    ------------------
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

### networks

The main purpose of the network is to run all the related containers in the same network so that they can communicate without any hindrance. the docker-compose file gives this functionality by default. It means all the containers running using a single docker-compose file can communicate with each other without specifying any other network details.
But if you still want to run your container in a particular network let say to make it compatible with the containers running using other docker-compose files then you can specify the network settings in the docker-compose file.

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
    networks:
      - ecommerce-network
  node-app:
    ------------------
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

### build

If we are building a docker image then there are two ways to use that in our docker-compose file. one, if we create the docker image using the ‘docker build’ command and then use the name of the command in the image key of the docker file as we saw in the third point. The other approach is to define all the details required to build the image in the docker-compose file itself.

We can pass these details using the build key. In the most simple way, we can just pass the path of Dockerfile to create the image in the build key

```
build: ./backend
```

Dockerfile is present in the backend folder (See the folder structure in figure 1).
If the Dockerfile is present in the same directory where the docker-compose file is present then we can use a dot (.)

```
build: .
```

But sometimes we have more than 1 docker file for the same container based on the profile (dev, test, prod, etc). So in that case we have to provide the relative path as well as the name of the Dockerfile.

```yaml
build: 
  context: ./backend
  dockerfile: Dockerfile-dev
```

context represents the directory that contains the Dockerfile and dockerfile key specifies the name of the file.

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
    networks:
      - ecommerce-network
  node-app:
    build:
      context: ./backend
      dockerfile: Dockerfile-dev
    ------------------
    ------------------
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

### ports

It is used to expose the ports, It maps the port number of the host machine to the port numbers of the container. so when user wants to interact with the container they can do so using the port numbers of the host machine

```yaml
ports:
- '3000:80'
- '8080:8080'
```

Here 3000 is the port number of the host machine and 80 is the port number of the container. So when we hit the 3000 port on the local machine it will redirect the request to the container on port 80. We can use the same port numbers as well, I used different values to show the difference properly. We can define multiple port mappings.

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
    networks:
      - ecommerce-network
  node-app:
    build:
      context: ./backend
      dockerfile: Dockerfile-dev
    ports:
      - '80:80'
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

### depends_on

By default, docker-compose tries to run all the services simultaneously. If we have some dependency between the modules then we have to specifically mention the dependency as the backend depends upon the MongoDB, we need to run MongoDB first then only the backend can run. So we add depend on the key in the backend service so that docker-compose will first start the MongoDB first and then only try to start backend service.

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
    networks:
      - ecommerce-network
  node-app:
    build:
      context: ./backend
      dockerfile: Dockerfile-dev
    ports:
      - '80:80'
    depends_on: 
      - mongodb
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

A single service can depend upon multiple services. So we can define multiple services names in the depends_on list.

### container_name

As we discussed in the second point that the name of the service is not equal to the name of the container. So, if we want to specify the name of the container then we can use the container_name key

```
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
    networks:
      - ecommerce-network
    container-name: mongodb
  node-app:
    build:
      context: ./backend
      dockerfile: Dockerfile-dev
    ports:
      - '80:80'
    depends_on: 
      - mongodb
    container-name: backend
  react-app:
    ------------------
    ------------------
    
volumes:
  data:
```

### stdin_open: and tty

It is used to add the interactive mode in the container. It is equal to the -it flag that we use in the docker run command.

```yaml
version: "3.8"
services:
  mongodb:
    image: 'mongo'
    volumes:
      - data:data/db
    env-file:
      - ./env/mongo.env
    networks:
      - ecommerce-network
    container-name: mongodb
  node-app:
    build:
      context: ./backend
      dockerfile: Dockerfile-dev
    ports:
      - '80:80'
    depends_on: 
      - mongodb
    container-name: backend
  react-app:
    stdin_open: true
    tty: true
    ------------------
    
volumes:
  data:
```

So our final docker-file will look something like this:

## Starting services using docker-compose

First, in the console, we have to navigate to the folder where the docker-compose file is present. Then there is only 1 basic command

```
docker-compose up
```

This command will first check if all the images mentioned in the docker-compose file are present locally or not. If not then it will pull it from the docker repository and then it will automatically run it. By default, docker-compose tries to run all the containers at the same time.
We can add some extra parameters in the given docker-compose up command
Detach mode (-d)
If we want to run our containers in detach mode then we can add ‘-d’ in the docker-compose command.

```
docker-compose up -d
```

Forcing image rebuild ( — build)
If we want to rebuild the image every time we run the docker-compose file then we can use — build with the docker-compose up command
docker-compose up --build
It will only rebuild the image from the Dockerfile present in the docker-compose file, docker-compose cannot rebuild the image passed with the image key. like in our example we can rebuild the node-app image but cannot build the MongoDB image.
If we just want to build all the images present in the docker-compose file and not want to run the containers then we can use

```
docker-compose build
```

Stopping services using docker-compose
We can stop all services and remove all the containers from the local cache using.

```
docker-compose down
```

It also removes the default network is created during the startup time but it will not remove the volumes used in the docker-compose file by default.
Removing volumes while stopping services
We can add -v in the docker-compose down command to remove all the volumes defined in the docker-compose file.

```
docker-compose down -v
```

So that's all for this article, for more information about the docker-compose please refer to the official documentation: <https://docs.docker.com/compose/compose-file/>

# Docker Cheat Sheet

## Table of Contents

- [Why Docker](#why-docker)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Containers](#containers)
- [Images](#images)
- [Networks](#networks)
- [Registry and Repository](#registry--repository)
- [Dockerfile](#dockerfile)
- [Layers](#layers)
- [Links](#links)
- [Volumes](#volumes)
- [Exposing Ports](#exposing-ports)
- [Best Practices](#best-practices)
- [Docker-Compose](#docker-compose)
- [Security](#security)
- [Tips](#tips)
- [Contributing](#contributing)

## Why Docker

"With Docker, developers can build any app in any language using any toolchain. “Dockerized” apps are completely portable and can run anywhere - colleagues’ OS X and Windows laptops, QA servers running Ubuntu in the cloud, and production data center VMs running Red Hat.

Developers can get going quickly by starting with one of the 13,000+ apps available on Docker Hub. Docker manages and tracks changes and dependencies, making it easier for sysadmins to understand how the apps that developers build work. And with Docker Hub, developers can automate their build pipeline and share artifacts with collaborators through public or private repositories.

Docker helps developers build and ship higher-quality applications, faster." -- [What is Docker](https://www.docker.com/what-docker#copy1)

## Prerequisites

I use [Oh My Zsh](https://github.com/ohmyzsh/oh-my-zsh) with the [Docker plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) for autocompletion of docker commands. YMMV.

### Linux

The 3.10.x kernel is [the minimum requirement](https://docs.docker.com/engine/installation/binaries/#check-kernel-dependencies) for Docker.

### MacOS

10.8 “Mountain Lion” or newer is required.

### Windows 10

Hyper-V must be enabled in BIOS

VT-D must also be enabled if available (Intel Processors).

### Windows Server

Windows Server 2016 is the minimum version required to install docker and docker-compose. Limitations exist on this version, such as multiple virtual networks and Linux containers. Windows Server 2019 and later are recommended.

## Installation

### Linux

Run this quick and easy install script provided by Docker:

```sh
curl -sSL https://get.docker.com/ | sh
```

If you're not willing to run a random shell script, please see the [installation](https://docs.docker.com/engine/installation/linux/) instructions for your distribution.

If you are a complete Docker newbie, you should follow the [series of tutorials](https://docs.docker.com/engine/getstarted/) now.

### macOS

Download and install [Docker Community Edition](https://www.docker.com/community-edition). if you have Homebrew-Cask, just type `brew install --cask docker`. Or Download and install [Docker Toolbox](https://docs.docker.com/toolbox/overview/).  [Docker For Mac](https://docs.docker.com/docker-for-mac/) is nice, but it's not quite as finished as the VirtualBox install.  [See the comparison](https://docs.docker.com/docker-for-mac/docker-toolbox/).

> **NOTE** Docker Toolbox is legacy. You should to use Docker Community Edition, See [Docker Toolbox](https://docs.docker.com/toolbox/overview/).

Once you've installed Docker Community Edition, click the docker icon in Launchpad. Then start up a container:

```sh
docker run hello-world
```

That's it, you have a running Docker container.

If you are a complete Docker newbie, you should probably follow the [series of tutorials](https://docs.docker.com/engine/getstarted/) now.

### Windows 10

Instructions to install Docker Desktop for Windows can be found [here](https://docs.docker.com/desktop/windows/install/)

Once installed, open powershell as administrator and run:

```powershell
# Display the version of docker installed:
docker version

# Pull, create, and run 'hello-world':
docker run hello-world
```

To continue with this cheat sheet, right click the Docker icon in the system tray, and go to settings. In order to mount volumes, the C:/ drive will need to be enabled in the settings to that information can be passed into the containers (later described in this article).

To switch between Windows containers and Linux containers, right click the icon in the system tray and click the button to switch container operating system Doing this will stop the current containers that are running, and make them unaccessible until the container OS is switched back.

Additionally, if you have WSL or WSL2 installed on your desktop, you might want to install the Linux Kernel for Windows. Instructions can be found [here](https://techcommunity.microsoft.com/t5/windows-dev-appconsult/using-wsl2-in-a-docker-linux-container-on-windows-to-run-a/ba-p/1482133). This requires the Windows Subsystem for Linux feature. This will allow for containers to be accessed by WSL operating systems, as well as the efficiency gain from running WSL operating systems in docker. It is also preferred to use [Windows terminal](https://docs.microsoft.com/en-us/windows/terminal/get-started) for this.

### Windows Server 2016 / 2019

Follow Microsoft's instructions that can be found [here](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/deploy-containers-on-server#install-docker)

If using the latest edge version of 2019, be prepared to only work in powershell, as it is only a servercore image (no desktop interface). When starting this machine, it will login and go straight to a powershell window. It is reccomended to install text editors and other tools using [Chocolatey](https://chocolatey.org/install).

After installing, these commands will work:

```powershell
# Display the version of docker installed:
docker version

# Pull, create, and run 'hello-world':
docker run hello-world
```

Windows Server 2016 is not able to run Linux images.

Windows Server Build 2004 is capable of running both linux and windows containers simultaneously through Hyper-V isolation. When running containers, use the ```--isolation=hyperv``` command, which will isolate the container using a seperate kernel instance.

### Check Version

It is very important that you always know the current version of Docker you are currently running on at any point in time. This is very helpful because you get to know what features are compatible with what you have running. This is also important because you know what containers to run from the docker store when you are trying to get template containers. That said let see how to know which version of docker we have running currently.

- [`docker version`](https://docs.docker.com/engine/reference/commandline/version/) shows which version of docker you have running.

Get the server version:

```console
$ docker version --format '{{.Server.Version}}'
1.8.0
```

You can also dump raw JSON data:

```console
$ docker version --format '{{json .}}'
{"Client":{"Version":"1.8.0","ApiVersion":"1.20","GitCommit":"f5bae0a","GoVersion":"go1.4.2","Os":"linux","Arch":"am"}
```

## Containers

[Your basic isolated Docker process](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/). Containers are to Virtual Machines as threads are to processes. Or you can think of them as chroots on steroids.

### Lifecycle

- [`docker create`](https://docs.docker.com/engine/reference/commandline/create) creates a container but does not start it.
- [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/) allows the container to be renamed.
- [`docker run`](https://docs.docker.com/engine/reference/commandline/run) creates and starts a container in one operation.
- [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm) deletes a container.
- [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) updates a container's resource limits.

Normally if you run a container without options it will start and stop immediately, if you want keep it running you can use the command, `docker run -td container_id` this will use the option `-t` that will allocate a pseudo-TTY session and `-d` that will detach automatically the container (run container in background and print container ID).

If you want a transient container, `docker run --rm` will remove the container after it stops.

If you want to map a directory on the host to a docker container, `docker run -v $HOSTDIR:$DOCKERDIR`. Also see [Volumes](https://github.com/wsargent/docker-cheat-sheet/#volumes).

If you want to remove also the volumes associated with the container, the deletion of the container must include the `-v` switch like in `docker rm -v`.

There's also a [logging driver](https://docs.docker.com/engine/admin/logging/overview/) available for individual containers in docker 1.10. To run docker with a custom log driver (i.e., to syslog), use `docker run --log-driver=syslog`.

Another useful option is `docker run --name yourname docker_image` because when you specify the `--name` inside the run command this will allow you to start and stop a container by calling it with the name the you specified when you created it.

### Starting and Stopping

- [`docker start`](https://docs.docker.com/engine/reference/commandline/start) starts a container so it is running.
- [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop) stops a running container.
- [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart) stops and starts a container.
- [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/) pauses a running container, "freezing" it in place.
- [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) will unpause a running container.
- [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait) blocks until running container stops.
- [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill) sends a SIGKILL to a running container.
- [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach) will connect to a running container.

If you want to detach from a running container, use `Ctrl + p, Ctrl + q`.
If you want to integrate a container with a [host process manager](https://docs.docker.com/engine/admin/host_integration/), start the daemon with `-r=false` then use `docker start -a`.

If you want to expose container ports through the host, see the [exposing ports](#exposing-ports) section.

Restart policies on crashed docker instances are [covered here](http://container42.com/2014/09/30/docker-restart-policies/).

#### CPU Constraints

You can limit CPU, either using a percentage of all CPUs, or by using specific cores.  

For example, you can tell the [`cpu-shares`](https://docs.docker.com/engine/reference/run/#/cpu-share-constraint) setting.  The setting is a bit strange -- 1024 means 100% of the CPU, so if you want the container to take 50% of all CPU cores, you should specify 512.  See <https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu> for more:

```sh
docker run -it -c 512 agileek/cpuset-test
```

You can also only use some CPU cores using [`cpuset-cpus`](https://docs.docker.com/engine/reference/run/#/cpuset-constraint).  See <https://agileek.github.io/docker/2014/08/06/docker-cpuset/> for details and some nice videos:

```sh
docker run -it --cpuset-cpus=0,4,6 agileek/cpuset-test
```

Note that Docker can still **see** all of the CPUs inside the container -- it just isn't using all of them.  See <https://github.com/docker/docker/issues/20770> for more details.

#### Memory Constraints

You can also set [memory constraints](https://docs.docker.com/engine/reference/run/#/user-memory-constraints) on Docker:

```sh
docker run -it -m 300M ubuntu:14.04 /bin/bash
```

#### Capabilities

Linux capabilities can be set by using `cap-add` and `cap-drop`.  See <https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities> for details.  This should be used for greater security.

To mount a FUSE based filesystem, you need to combine both --cap-add and --device:

```sh
docker run --rm -it --cap-add SYS_ADMIN --device /dev/fuse sshfs
```

Give access to a single device:

```sh
docker run -it --device=/dev/ttyUSB0 debian bash
```

Give access to all devices:

```sh
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb debian bash
```

More info about privileged containers [here](
<https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities>).

### Info

- [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps) shows running containers.
- [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs) gets logs from container.  (You can use a custom log driver, but logs is only available for `json-file` and `journald` in 1.10).
- [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) looks at all the info on a container (including IP address).
- [`docker events`](https://docs.docker.com/engine/reference/commandline/events) gets events from container.
- [`docker port`](https://docs.docker.com/engine/reference/commandline/port) shows public facing port of container.
- [`docker top`](https://docs.docker.com/engine/reference/commandline/top) shows running processes in container.
- [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats) shows containers' resource usage statistics.
- [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff) shows changed files in the container's FS.

`docker ps -a` shows running and stopped containers.

`docker stats --all` shows a list of all containers, default shows just running.

### Import / Export

- [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp) copies files or folders between a container and the local filesystem.
- [`docker export`](https://docs.docker.com/engine/reference/commandline/export) turns container filesystem into tarball archive stream to STDOUT.

### Executing Commands

- [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec) to execute a command in container.

To enter a running container, attach a new shell process to a running container called foo, use: `docker exec -it foo /bin/bash`.

## Images

Images are just [templates for docker containers](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Lifecycle

- [`docker images`](https://docs.docker.com/engine/reference/commandline/images) shows all images.
- [`docker import`](https://docs.docker.com/engine/reference/commandline/import) creates an image from a tarball.
- [`docker build`](https://docs.docker.com/engine/reference/commandline/build) creates image from Dockerfile.
- [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) creates image from a container, pausing it temporarily if it is running.
- [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) removes an image.
- [`docker load`](https://docs.docker.com/engine/reference/commandline/load) loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
- [`docker save`](https://docs.docker.com/engine/reference/commandline/save) saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).

### Info

- [`docker history`](https://docs.docker.com/engine/reference/commandline/history) shows history of image.
- [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) tags an image to a name (local or registry).

### Cleaning up

While you can use the `docker rmi` command to remove specific images, there's a tool called [docker-gc](https://github.com/spotify/docker-gc) that will safely clean up images that are no longer used by any containers. As of docker 1.13, `docker image prune` is also available for removing unused images. See [Prune](#prune).

### Load/Save image

Load an image from file:

```sh
docker load < my_image.tar.gz
```

Save an existing image:

```sh
docker save my_image:my_tag | gzip > my_image.tar.gz
```

### Import/Export container

Import a container as an image from file:

```sh
cat my_container.tar.gz | docker import - my_image:my_tag
```

Export an existing container:

```sh
docker export my_container | gzip > my_container.tar.gz
```

### Difference between loading a saved image and importing an exported container as an image

Loading an image using the `load` command creates a new image including its history.  
Importing a container as an image using the `import` command creates a new image excluding the history which results in a smaller image size compared to loading an image.

## Networks

Docker has a [networks](https://docs.docker.com/engine/userguide/networking/) feature. Docker automatically creates 3 network interfaces when you install it (bridge, host none). A new container is launched into the bridge network by default. To enable communication between multiple containers, you can create a new network and launch containers in it. This enables containers to communicate to each other while being isolated from containers that are not connected to the network. Furthermore, it allows to map container names to their IP addresses. See [working with networks](https://docs.docker.com/engine/userguide/networking/work-with-networks/) for more details.

### Lifecycle

- [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/) NAME Create a new network (default type: bridge).
- [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/) NAME Remove one or more networks by name or identifier. No containers can be connected to the network when deleting it.

### Info

- [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/) List networks
- [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/) NAME Display detailed information on one or more networks.

### Connection

- [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/) NETWORK CONTAINER Connect a container to a network
- [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/) NETWORK CONTAINER Disconnect a container from a network

You can specify a [specific IP address for a container](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```sh
# create a new bridge network with your subnet and gateway for your ip block
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# run a nginx container with a specific ip in that block
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# curl the ip from any other place (assuming this is a public ip block duh)
$ curl 203.0.113.2
```

## Registry & Repository

A repository is a *hosted* collection of tagged images that together create the file system for a container.

A registry is a *host* -- a server that stores repositories and provides an HTTP API for [managing the uploading and downloading of repositories](https://docs.docker.com/engine/tutorials/dockerrepos/).

Docker.com hosts its own [index](https://hub.docker.com/) to a central registry which contains a large number of repositories.  Having said that, the central docker registry [does not do a good job of verifying images](https://titanous.com/posts/docker-insecurity) and should be avoided if you're worried about security.

- [`docker login`](https://docs.docker.com/engine/reference/commandline/login) to login to a registry.
- [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) to logout from a registry.
- [`docker search`](https://docs.docker.com/engine/reference/commandline/search) searches registry for image.
- [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) pulls an image from registry to local machine.
- [`docker push`](https://docs.docker.com/engine/reference/commandline/push) pushes an image to the registry from local machine.

### Run local registry

You can run a local registry by using the [docker distribution](https://github.com/docker/distribution) project and looking at the [local deploy](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md) instructions.

Also see the [mailing list](https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution).

## Dockerfile

[The configuration file](https://docs.docker.com/engine/reference/builder/). Sets up a Docker container when you run `docker build` on it. Vastly preferable to `docker commit`.  

Here are some common text editors and their syntax highlighting modules you could use to create Dockerfiles:

- If you use [jEdit](http://jedit.org), I've put up a syntax highlighting module for [Dockerfile](https://github.com/wsargent/jedit-docker-mode) you can use.
- [Sublime Text 2](https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting)
- [Atom](https://atom.io/packages/language-docker)
- [Vim](https://github.com/ekalinin/Dockerfile.vim)
- [Emacs](https://github.com/spotify/dockerfile-mode)
- [TextMate](https://github.com/docker/docker/tree/master/contrib/syntax/textmate)
- [VS Code](https://github.com/Microsoft/vscode-docker)
- Also see [Docker meets the IDE](https://domeide.github.io/)

### Instructions

- [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
- [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
- [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Set the Author field of the generated images.
- [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
- [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
- [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime.  NOTE: does not actually make ports accessible.
- [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
- [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.
- [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container.  By default this copies as root regardless of the USER/WORKDIR settings.  Use `--chown=<user>:<group>` to give ownership to another user/group.  (Same for `ADD`.)
- [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
- [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
- [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
- [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
- [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
- [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
- [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
- [LABEL](https://docs.docker.com/config/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.
- [SHELL](https://docs.docker.com/engine/reference/builder/#shell) override default shell is used by docker to run commands.
- [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) tells docker how to test a container to check that it is still working.

### Tutorial

- [Flux7's Dockerfile Tutorial](https://www.flux7.com/tutorial/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### Examples

- [Examples](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)
- [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
- [Michael Crosby](http://crosbymichael.com/) has some more [Dockerfiles best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).
- [Building Good Docker Images](http://jonathan.bergknoff.com/journal/building-good-docker-images) / [Building Better Docker Images](http://jonathan.bergknoff.com/journal/building-better-docker-images)
- [Managing Container Configuration with Metadata](https://speakerdeck.com/garethr/managing-container-configuration-with-metadata)
- [How to write excellent Dockerfiles](https://rock-it.pl/how-to-write-excellent-dockerfiles/)

## Layers

The versioned filesystem in Docker is based on layers. They're like [git commits or changesets for filesystems](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/).

## Links

Links are how Docker containers talk to each other [through TCP/IP ports](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/). [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) show worked examples. You can also resolve [links by hostname](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#/updating-the-etchosts-file).

This has been deprecated to some extent by [user-defined networks](https://docs.docker.com/network/).

NOTE: If you want containers to ONLY communicate with each other through links, start the docker daemon with `-icc=false` to disable inter process communication.

If you have a container with the name CONTAINER (specified by `docker run --name CONTAINER`) and in the Dockerfile, it has an exposed port:

```
EXPOSE 1337
```

Then if we create another container called LINKED like so:

```sh
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

Then the exposed ports and aliases of CONTAINER will show up in LINKED with the following environment variables:

```sh
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

And you can connect to it that way.

To delete links, use `docker rm --link`.

Generally, linking between docker services is a subset of "service discovery", a big problem if you're planning to use Docker at scale in production.  Please read [The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores) for more info.

## Volumes

Docker volumes are [free-floating filesystems](https://docs.docker.com/engine/tutorials/dockervolumes/). They don't have to be connected to a particular container. You can use volumes mounted from [data-only containers](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e) for portability. As of Docker 1.9.0, Docker has named volumes which replace data-only containers. Consider using named volumes to implement it rather than data containers.

### Lifecycle

- [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
- [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### Info

- [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
- [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

Volumes are useful in situations where you can't use links (which are TCP/IP only). For instance, if you need to have two docker instances communicate by leaving stuff on the filesystem.

You can mount them in several docker containers at once, using `docker run --volumes-from`.

Because volumes are isolated filesystems, they are often used to store state from computations between transient containers. That is, you can have a stateless and transient container run from a recipe, blow it away, and then have a second instance of the transient container pick up from where the last one left off.

See [advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html) for more details. [Container42](http://container42.com/2014/11/03/docker-indepth-volumes/) is also helpful.

You can [map MacOS host directories as docker volumes](https://docs.docker.com/engine/tutorials/dockervolumes/#mount-a-host-directory-as-a-data-volume):

```sh
docker run -v /Users/wsargent/myapp/src:/src
```

You can use remote NFS volumes if you're [feeling brave](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume).

You may also consider running data-only containers as described [here](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) to provide some data portability.

Be aware that you can [mount files as volumes](#volumes-can-be-files).

## Exposing ports

Exposing incoming ports through the host container is [fiddly but doable](https://docs.docker.com/engine/reference/run/#expose-incoming-ports).

This is done by mapping the container port to the host port (only using localhost interface) using `-p`:

```sh
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT \
  --name CONTAINER \
  -t someimage
```

You can tell Docker that the container listens on the specified network ports at runtime by using [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose):

```Dockerfile
EXPOSE <CONTAINERPORT>
```

Note that `EXPOSE` does not expose the port itself - only `-p` will do that.

To expose the container's port on your localhost's port, run:

```sh
iptables -t nat -A DOCKER -p tcp --dport <LOCALHOSTPORT> -j DNAT --to-destination <CONTAINERIP>:<PORT>
```

If you're running Docker in Virtualbox, you then need to forward the port there as well, using [forwarded_port](https://docs.vagrantup.com/v2/networking/forwarded_ports.html). Define a range of ports in your Vagrantfile like this so you can dynamically map them:

```
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host => port, :guest => port
  end

  ...
end
```

If you forget what you mapped the port to on the host container, use `docker port` to show it:

```sh
docker port CONTAINER $CONTAINERPORT
```

## Best Practices

This is where general Docker best practices and war stories go:

- [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
- [Bridget Kromhout](https://twitter.com/bridgetkromhout) has a useful blog post on [running Docker in production](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html) at Dramafever.
- There's also a best practices [blog post](http://developers.lyst.com/devops/2014/12/08/docker/) from Lyst.
- [Building a Development Environment With Docker](https://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
- [Discourse in a Docker Container](https://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## Docker-Compose

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To learn more about all the features of Compose, see the [list of features](https://docs.docker.com/compose/overview/#features).

By using the following command you can start up your application:

```sh
docker-compose -f <docker-compose-file> up
```

You can also run docker-compose in detached mode using -d flag, then you can stop it whenever needed by the following command:

```sh
docker-compose stop
```

You can bring everything down, removing the containers entirely, with the down command. Pass `--volumes` to also remove the data volume.

## Security

This is where security tips about Docker go. The Docker [security](https://docs.docker.com/engine/security/security/) page goes into more detail.

First things first: Docker runs as root. If you are in the `docker` group, you effectively [have root access](https://web.archive.org/web/20161226211755/http://reventlov.com/advisories/using-the-docker-command-to-root-the-host). If you expose the docker unix socket to a container, you are giving the container [root access to the host](https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container/).

Docker should not be your only defense. You should secure and harden it.

For an understanding of what containers leave exposed, you should read [Understanding and Hardening Linux Containers](https://www.nccgroup.trust/globalassets/our-research/us/whitepapers/2016/april/ncc_group_understanding_hardening_linux_containers-1-1.pdf) by [Aaron Grattafiori](https://twitter.com/dyn___). This is a complete and comprehensive guide to the issues involved with containers, with a plethora of links and footnotes leading on to yet more useful content. The security tips following are useful if you've already hardened containers in the past, but are not a substitute for understanding.

### Security Tips

For greatest security, you want to run Docker inside a virtual machine. This is straight from the Docker Security Team Lead -- [slides](http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security) / [notes](http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/). Then, run with AppArmor / seccomp / SELinux / grsec etc to [limit the container permissions](http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/). See the [Docker 1.10 security features](https://blog.docker.com/2016/02/docker-engine-1-10-security/) for more details.

Docker image ids are [sensitive information](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4) and should not be exposed to the outside world. Treat them like passwords.

See the [Docker Security Cheat Sheet](https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.adoc) by [Thomas Sjögren](https://github.com/konstruktoid): some good stuff about container hardening in there.

Check out the [docker bench security script](https://github.com/docker/docker-bench-security), download the [white papers](https://blog.docker.com/2015/05/understanding-docker-security-and-best-practices/).

Snyk's [10 Docker Image Security Best Practices cheat sheet](https://snyk.io/blog/10-docker-image-security-best-practices/)

You should start off by using a kernel with unstable patches for grsecurity / pax compiled in, such as [Alpine Linux](https://en.wikipedia.org/wiki/Alpine_Linux). If you are using grsecurity in production, you should spring for [commercial support](https://grsecurity.net/business_support.php) for the [stable patches](https://grsecurity.net/announce.php), same as you would do for RedHat. It's $200 a month, which is nothing to your devops budget.

Since docker 1.11 you can easily limit the number of active processes running inside a container to prevent fork bombs. This requires a linux kernel >= 4.3 with CGROUP_PIDS=y to be in the kernel configuration.

```sb
docker run --pids-limit=64
```

Also available since docker 1.11 is the ability to prevent processes from gaining new privileges. This feature have been in the linux kernel since version 3.5. You can read more about it in [this](http://www.projectatomic.io/blog/2016/03/no-new-privs-docker/) blog post.

```sh
docker run --security-opt=no-new-privileges
```

From the [Docker Security Cheat Sheet](http://container-solutions.com/content/uploads/2015/06/15.06.15_DockerCheatSheet_A2.pdf) (it's in PDF which makes it hard to use, so copying below) by [Container Solutions](http://container-solutions.com/is-docker-safe-for-production/):

Turn off interprocess communication with:

```sh
docker -d --icc=false --iptables
```

Set the container to be read-only:

```sh
docker run --read-only
```

Verify images with a hashsum:

```sh
docker pull debian@sha256:a25306f3850e1bd44541976aa7b5fd0a29be
```

Set volumes to be read only:

```sh
docker run -v $(pwd)/secrets:/secrets:ro debian
```

Define and run a user in your Dockerfile so you don't run as root inside the container:

```Dockerfile
RUN groupadd -r user && useradd -r -g user user
USER user
```

### User Namespaces

There's also work on [user namespaces](https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/) -- it is in 1.10 but is not enabled by default.

To enable user namespaces ("remap the userns") in Ubuntu 15.10, [follow the blog example](https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/).

### Security Videos

- [Using Docker Safely](https://youtu.be/04LOuMgNj9U)
- [Securing your applications using Docker](https://youtu.be/KmxOXmPhZbk)
- [Container security: Do containers actually contain?](https://youtu.be/a9lE9Urr6AQ)
- [Linux Containers: Future or Fantasy?](https://www.youtube.com/watch?v=iN6QbszB1R8)

### Security Roadmap

The Docker roadmap talks about [seccomp support](https://github.com/docker/docker/blob/master/ROADMAP.md#11-security).
There is an AppArmor policy generator called [bane](https://github.com/jfrazelle/bane), and they're working on [security profiles](https://github.com/docker/docker/issues/17142).

## Tips

Sources:

- [15 Docker Tips in 5 minutes](http://sssslide.com/speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)
- [CodeFresh Everyday Hacks Docker](https://codefresh.io/blog/everyday-hacks-docker/)

### Prune

The new [Data Management Commands](https://github.com/docker/docker/pull/26108) have landed as of Docker 1.13:

- `docker system prune`
- `docker volume prune`
- `docker network prune`
- `docker container prune`
- `docker image prune`

### df

`docker system df` presents a summary of the space currently used by different docker objects.

### Heredoc Docker Container

```sh
docker build -t htop - << EOF
FROM alpine
RUN apk --no-cache add htop
EOF
```

### Last IDs

```sh
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit $(dl) helloworld
```

### Commit with command (needs Dockerfile)

```sh
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' $(dl) postgres
```

### Get IP address

```sh
docker inspect $(dl) | grep -wm1 IPAddress | cut -d '"' -f 4
```

Or with [jq](https://stedolan.github.io/jq/) installed:

```sh
docker inspect $(dl) | jq -r '.[0].NetworkSettings.IPAddress'
```

Or using a [go template](https://docs.docker.com/engine/reference/commandline/inspect):

```sh
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

Or when building an image from Dockerfile, when you want to pass in a build argument:

```sh
DOCKER_HOST_IP=`ifconfig | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" | grep -v 127.0.0.1 | awk '{ print $2 }' | cut -f2 -d: | head -n1`
echo DOCKER_HOST_IP = $DOCKER_HOST_IP
docker build \
  --build-arg ARTIFACTORY_ADDRESS=$DOCKER_HOST_IP 
  -t sometag \
  some-directory/
```

### Get port mapping

```sh
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>
```

### Find containers by regular expression

```sh
for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done
```

### Get Environment Settings

```sh
docker run --rm ubuntu env
```

### Kill running containers

```sh
docker kill $(docker ps -q)
```

### Delete all containers (force!! running or stopped containers)

```sh
docker rm -f $(docker ps -qa)
```

### Delete old containers

```sh
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### Delete stopped containers

```sh
docker rm -v $(docker ps -a -q -f status=exited)
```

### Delete containers after stopping

```sh
docker stop $(docker ps -aq) && docker rm -v $(docker ps -aq)
```

### Delete dangling images

```sh
docker rmi $(docker images -q -f dangling=true)
```

### Delete all images

```sh
docker rmi $(docker images -q)
```

### Delete dangling volumes

As of Docker 1.9:

```sh
docker volume rm $(docker volume ls -q -f dangling=true)
```

In 1.9.0, the filter `dangling=false` does *not* work - it is ignored and will list all volumes.

### Show image dependencies

```sh
docker images -viz | dot -Tpng -o docker.png
```

### Slimming down Docker containers

- Cleaning APT in a `RUN` layer - This should be done in the same layer as other `apt` commands. Otherwise, the previous layers still persist the original information and your images will still be fat.

    ```Dockerfile
    RUN {apt commands} \
      && apt-get clean \
      && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
    ```

- Flatten an image

    ```sh
    ID=$(docker run -d image-name /bin/bash)
    docker export $ID | docker import – flat-image-name
    ```

- For backup

    ```sh
    ID=$(docker run -d image-name /bin/bash)
    (docker export $ID | gzip -c > image.tgz)
    gzip -dc image.tgz | docker import - flat-image-name
    ```

### Monitor system resource utilization for running containers

To check the CPU, memory, and network I/O usage of a single container, you can use:

```sh
docker stats <container>
```

For all containers listed by ID:

```sh
docker stats $(docker ps -q)
```

For all containers listed by name:

```sh
docker stats $(docker ps --format '{{.Names}}')
```

For all containers listed by image:

```sh
docker ps -a -f ancestor=ubuntu
```

Remove all untagged images:

```sh
docker rmi $(docker images | grep “^” | awk '{split($0,a," "); print a[3]}')
```

Remove container by a regular expression:

```sh
docker ps -a | grep wildfly | awk '{print $1}' | xargs docker rm -f
```

Remove all exited containers:

```sh
docker rm -f $(docker ps -a | grep Exit | awk '{ print $1 }')
```

### Volumes can be files

Be aware that you can mount files as volumes. For example you can inject a configuration file like this:

```sh
# copy file from container
docker run --rm httpd cat /usr/local/apache2/conf/httpd.conf > httpd.conf

# edit file
vim httpd.conf

# start container with modified configuration
docker run --rm -it -v "$PWD/httpd.conf:/usr/local/apache2/conf/httpd.conf:ro" -p "80:80" httpd
```


## Dockerfile
Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.


1. FROM

Usage:

```
FROM <image>
FROM <image>:<tag>
FROM <image>@<digest>
```

- FROM must be the first non-comment instruction in the Dockerfile.
- FROM can appear multiple times within a single Dockerfile in order to create multiple images. Simply make a note of the last image ID output by the commit before each new FROM command.
- The tag or digest values are optional. If you omit either of them, the builder assumes a latest by default. The builder returns an error if it cannot match the tag value.

2. MAINTAINER

Usage:

```
MAINTAINER <name>
```

- The MAINTAINER instruction allows you to set the Author field of the generated images.

3. RUN

Usage:

```
RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
RUN ["<executable>", "<param1>", "<param2>"] (exec form)

# whitespace in instruction arguments, such as the commands following RUN, are preserved, so the following example prints ` hello world` with leading whitespace as specified:

RUN echo "\
     hello\
     world"
```

- The exec form makes it possible to avoid shell string munging, and to RUN commands using a base image that does not contain the specified shell executable.
- The default shell for the shell form can be changed using the SHELL command.
- Normal shell processing does not occur when using the exec form. For example, RUN ["echo", "$HOME"] will not do variable substitution on $HOME.

4. CMD

Usage:

```
CMD ["<executable>","<param1>","<param2>"] (exec form, this is the preferred form)
CMD ["<param1>","<param2>"] (as default parameters to ENTRYPOINT)
CMD <command> <param1> <param2> (shell form)

CMD echo FIRST COMMAND;echo SECOND COMMAND
CMD ["/bin/bash", "-c", "echo FIRST COMMAND;echo SECOND COMMAND"]

```

- The main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable, or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.
- There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.
- If CMD is used to provide default arguments for the ENTRYPOINT instruction, both the CMD and ENTRYPOINT instructions should be specified with the JSON array format.
- If the user specifies arguments to docker run then they will override the default specified in CMD.
- Normal shell processing does not occur when using the exec form. For example, CMD ["echo", "$HOME"] will not do variable substitution on $HOME.

The CMD directive can be used in two ways:

    To define an executable with its arguments that the container should run. In this case, we can omit the ENTRYPOINT directive.
    To provide additional arguments to the executable defined in the ENTRYPOINT directive

The sh -c command accepts a list of commands and executes them. Moreover, the commands can be separated with the known shell operators:

    the semicolon (;) operator
    the ambersand (&) operator
    the AND (&&) operator
    the OR (||) operator
    the pipe (|)

The sh -c command provides us the means to execute multiple commands.

We should note that the CMD and ENTRYPOINT directives can't be used as a replacement for each other. They both serve different purposes. But, running multiple commands in ENTRYPOINT and CMD follows the same syntax. 

10. ENTRYPOINT

Usage:

```
# The exec form, which is the preferred form:
ENTRYPOINT ["<executable>", "<param1>", "<param2>"] (exec form, preferred)

# The shell form:
ENTRYPOINT <command> <param1> <param2> (shell form)
```

- Allows you to configure a container that will run as an executable.
- Command line arguments to docker run <image> will be appended after all elements in an exec form ENTRYPOINT and will override all elements specified using CMD.
- The shell form prevents any CMD or run command line arguments from being used, but the ENTRYPOINT will start via the shell. This means the executable will not be PID 1 nor will it receive UNIX signals. Prepend exec to get around this drawback.
- Only the last ENTRYPOINT instruction in the Dockerfile will have an effect.

1. LABEL

Usage:

```
LABEL <key>=<value> [<key>=<value> ...]
```

- The LABEL instruction adds metadata to an image.
- To include spaces within a LABEL value, use quotes and backslashes as you would in command-line parsing.
- Labels are additive including LABELs in FROM images.
- If Docker encounters a label/key that already exists, the new value overrides any previous labels with identical keys.
- To view an image’s labels, use the docker inspect command. They will be under the "Labels" JSON attribute.

6. EXPOSE

Usage:

```
EXPOSE <port> [<port> ...]
```

- Informs Docker that the container listens on the specified network port(s) at runtime.
- EXPOSE does not make the ports of the container accessible to the host.

7. ENV

Usage:

```
ENV <key> <value>
ENV <key>=<value> [<key>=<value> ...]
```

- The ENV instruction sets the environment variable `<key>` to the value `<value>`.
- The value will be in the environment of all “descendant” Dockerfile commands and can be replaced inline as well.
- The environment variables set using ENV will persist when a container is run from the resulting image.
- The first form will set a single variable to a value with the entire string after the first space being treated as the `<value>` - including characters such as spaces and quotes.

8. ADD

Usage:

```
ADD <src> [<src> ...] <dest>
ADD ["<src>", ... "<dest>"] (this form is required for paths containing whitespace)
```

- Copies new files, directories, or remote file URLs from `<src>` and adds them to the filesystem of the image at the path `<dest>`.
- `<dest>` is an absolute path, or a path relative to WORKDIR.
- If `<dest>` doesn’t exist, it is created along with all missing directories in its path.

9. COPY

Usage:

```
COPY <src> [<src> ...] <dest>
COPY ["<src>", ... "<dest>"] (this form is required for paths containing whitespace)
```

- Copies new files or directories from <src> and adds them to the filesystem of the image at the path `<dest>`.
- `<src>` may contain wildcards and matching will be done using Go’s filepath.Match rules.
- `<src>`must be relative to the source directory that is being built (the context of the build).
- `<dest>`is an absolute path, or a path relative to WORKDIR.
- If `<dest>`doesn’t exist, it is created along with all missing directories in its path.



11. VOLUME

Usage:

```
VOLUME ["<path>", ...]
VOLUME <path> [<path> ...]
```

- Creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers.

12. USER

Usage:

```
USER <username | UID>
```

The USER instruction sets the user name or UID to use when running the image and for any RUN, CMD and ENTRYPOINT instructions that follow it in the Dockerfile.

13. WORKDIR

Usage:

```
WORKDIR </path/to/workdir>
```

    - Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow it.
    - It can be used multiple times in the one Dockerfile. If a relative path is provided, it will be relative to the path of the previous WORKDIR instruction.

14. ARG

Usage:

```
ARG <name>[=<default value>]
```

- Defines a variable that users can pass at build-time to the builder with the docker build command using the `--build-arg <varname>=<value>` flag.
- Multiple variables may be defined by specifying ARG multiple times.
- It is not recommended to use build-time variables for passing secrets like github keys, user credentials, etc. Build-time variable values are visible to any user of the image with the docker history command.
- Environment variables defined using the ENV instruction always override an ARG instruction of the same name.
- Docker has a set of predefined ARG variables that you can use without a corresponding ARG instruction in the Dockerfile.
  - `HTTP_PROXY` and `http_proxy`
  - `HTTPS_PROXY` and `https_proxy`
  - `FTP_PROXY` and `ftp_proxy`
  - `NO_PROXY` and `no_proxy`
  - `ALL_PROXY` and `all_proxy`

To use these, pass them on the command line using the --build-arg flag, 
```
docker build --build-arg HTTPS_PROXY=https://my-proxy.example.com .
111

15. ONBUILD

Usage:

```
ONBUILD <Dockerfile INSTRUCTION>
```


- Adds to the image a trigger instruction to be executed at a later time, when the image is used as the base for another build. The trigger will be executed in the context of the downstream build, as if it had been inserted immediately after the `FROM` instruction in the downstream Dockerfile.
- Any build instruction can be registered as a trigger.
- Triggers are inherited by the "child" build only. In other words, they are not inherited by "grand-children" builds.
- The ONBUILD instruction may not trigger FROM, MAINTAINER, or ONBUILD instructions.
-

1.  STOPSIGNAL

Usage:

```
STOPSIGNAL <signal>
```

The STOPSIGNAL instruction sets the system call signal that will be sent to the container to exit. This signal can be a valid unsigned number that matches a position in the kernel’s syscall table, for instance 9, or a signal name in the format SIGNAME, for instance SIGKILL.

17. HEALTHCHECK

Usage:

```
HEALTHCHECK [<options>] CMD <command> (check container health by running a command inside the container)
HEALTHCHECK NONE (disable any healthcheck inherited from the base image)
```

- Tells Docker how to test a container to check that it is still working
- Whenever a health check passes, it becomes healthy. After a certain number of consecutive failures, it becomes unhealthy.
- The `<options>` that can appear are...
  - `--interval=<duration>`(default: 30s)
  - `--timeout=<duration>` (default: 30s)
  - `--retries=<number>` (default: 3)
- The health check will first run interval seconds after the container is started, and then again interval seconds after each previous check completes. If a single run of the check takes longer than timeout seconds then the check is considered to have failed. It takes retries consecutive failures of the health check for the container to be considered unhealthy.
- There can only be one HEALTHCHECK instruction in a Dockerfile. If you list more than one then only the last HEALTHCHECK will take effect.
- `<command>`can be either a shell command or an exec JSON array.
- The command's exit status indicates the health status of the container.
  - 0: success - the container is healthy and ready for use
  - 1: unhealthy - the container is not working correctly
  - 2: reserved - do not use this exit code
- The first 4096 bytes of stdout and stderr from the `<command>` are stored and can be queried with docker inspect.
- When the health status of a container changes, a health_status event is generated with the new status.

18, SHELL

Usage:

```
SHELL ["<executable>", "<param1>", "<param2>"]
```

- Allows the default shell used for the shell form of commands to be overridden.
- Each SHELL instruction overrides all previous SHELL instructions, and affects all subsequent instructions.
- Allows an alternate shell be used such as zsh, csh, tcsh, powershell, and others.
