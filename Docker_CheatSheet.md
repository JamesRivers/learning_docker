[![](https://awesomeopensource.com/awesome.gif)](https://awesomeopensource.com "Awesome Open Source Home")


# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#table-of-contents)Table of Contents

-   [Installation](https://awesomeopensource.com/project/eon01/DockerCheatSheet#installation)
-   [Docker Registries & Repositories](https://awesomeopensource.com/project/eon01/DockerCheatSheet#docker-registries--repositories)
-   [Running Containers](https://awesomeopensource.com/project/eon01/DockerCheatSheet#running-containers)
-   [Starting & Stopping Containers](https://awesomeopensource.com/project/eon01/DockerCheatSheet#starting--stopping-containers)
-   [Getting Information about Containers](https://awesomeopensource.com/project/eon01/DockerCheatSheet#getting-information-about-containers)
-   [Networking](https://awesomeopensource.com/project/eon01/DockerCheatSheet#networking)
-   [Security](https://awesomeopensource.com/project/eon01/DockerCheatSheet#security)
-   [Cleaning Docker](https://awesomeopensource.com/project/eon01/DockerCheatSheet#cleaning-docker)
-   [Docker Swarm](https://awesomeopensource.com/project/eon01/DockerCheatSheet#docker-swarm)
-   [Notes](https://awesomeopensource.com/project/eon01/DockerCheatSheet#notes)

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#the-ultimate-docker-cheat-sheet)The Ultimate Docker Cheat Sheet

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#installation)Installation

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#linux)Linux

For more information, see [here](https://docs.docker.com/install/#server)

```
curl -sSL https://get.docker.com/ | sh
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#mac)Mac

For more information, see [here](https://docs.docker.com/docker-for-mac/install/)

Use this link to download the dmg.

```
https://download.docker.com/mac/stable/Docker.dmg
```

Open the downloaded file and follow the installation instructions.

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#windows)Windows

For more information, see [here](https://docs.docker.com/docker-for-windows/install/)

Use the msi installer:

```
https://download.docker.com/win/stable/InstallDocker.msi
```

Open the downloaded file and follow the installation instructions.

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#docker-registries--repositories)Docker Registries & Repositories

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#login-to-a-registry)Login to a Registry

```
docker login
```

```
docker login localhost:8080
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#logout-from-a-registry)Logout from a Registry.

```
docker logout
```

```
docker logout localhost:8080
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#searching-an-image)Searching an Image

```
docker search nginx
```

```
docker search --filter stars=3 --no-trunc nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#pulling-an-image)Pulling an Image

```
docker image pull nginx
```

```
docker image pull eon01/nginx localhost:5000/myadmin/nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#pushing-an-image)Pushing an Image

```
docker image push eon01/nginx
```

```
docker image push eon01/nginx localhost:5000/myadmin/nginx
```

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#running-containers)Running Containers

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#create-and-run-a-simple-container)Create and Run a Simple Container

> \-Start an [ubuntu:latest](https://hub.docker.com/_/ubuntu/) image
> 
> -   Bind the port `80` from the **CONTAINER** to port `3000` on the **HOST**
> -   Mount the current directory to `/data` on the CONTAINER
> -   Note: on **windows** you have to change `-v ${PWD}:/data` to `-v "C:\Data":/data`

```
docker container run --name infinite -it -p 3000:80 -v ${PWD}:/data ubuntu:latest
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-a-container)Creating a Container

```
docker container create -t -i eon01/infinite --name infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#running-a-container)Running a Container

```
docker container run -it --name infinite -d eon01/infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#renaming-a-container)Renaming a Container

```
docker container rename infinite infinity
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-a-container)Removing a Container

```
docker container rm infinite
```

A container can be removed only after stopping it using `docker stop` command. To avoid this, add the `--rm` flag while running the container.

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#updating-a-container)Updating a Container

```
docker container update --cpu-shares 512 -m 300M infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#running-a-command-within-a-running-container)Running a command within a running container

```
docker exec -it infinite sh
```

In the example above, `bash` can replace `sh` as an alternative (if the above is giving an error).

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#starting--stopping-containers)Starting & Stopping Containers

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#starting)Starting

```
docker container start nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#stopping)Stopping

```
docker container stop nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#restarting)Restarting

```
docker container restart nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#pausing)Pausing

```
docker container pause nginx

```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#unpausing)Unpausing

```
docker container unpause nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#blocking-a-container)Blocking a Container

```
docker container wait nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#sending-a-sigkill)Sending a SIGKILL

```
docker container kill nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#sending-another-signal)Sending another signal

```
docker container kill -s HUP nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#connecting-to-an-existing-container)Connecting to an Existing Container

```
docker container attach nginx
```

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#getting-information-about-containers)Getting Information about Containers

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#from-running-containers)From Running Containers

Shortest way:

```
docker ps
```

Alternative:

```
docker container ls
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#from-all-containers)From All containers.

```
docker ps -a
```

```
docker container ls -a
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#container-logs)Container Logs

```
docker logs infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#tail--f--containers-logs)'tail -f' Containers' Logs

```
docker container logs infinite -f
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#inspecting-containers)Inspecting Containers

```
docker container inspect infinite
```

```
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' $(docker ps -q)
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#containers-events)Containers Events

```
docker system events infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#public-ports)Public Ports

```
docker container port infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#running-processes)Running Processes

```
docker container top infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#container-resource-usage)Container Resource Usage

```
docker container stats infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#inspecting-changes-to-files-or-directories-on-a-containers-filesystem)Inspecting changes to files or directories on a container’s filesystem

```
docker container diff infinite
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#managing-images)Managing Images

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#listing-images)Listing Images

```
docker image ls
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#building-images)Building Images

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#from-a-dockerfile-in-the-current-directory)From a Dockerfile in the Current Directory

```
docker build .
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#from-a-remote-git-repository)From a Remote GIT Repository

```
docker build github.com/creack/docker-firefox
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#instead-of-specifying-a-context-you-can-pass-a-single-dockerfile-in-the-url-or-pipe-the-file-in-via-stdin)Instead of Specifying a Context, You Can Pass a Single Dockerfile in the URL or Pipe the File in via STDIN

```
docker build - < Dockerfile
```

```
docker build - < context.tar.gz
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#building-and-tagging)Building and Tagging

```
docker build -t eon/infinite .
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#building-a-dockerfile-while-specifying-the-build-context)Building a Dockerfile while Specifying the Build Context

```
docker build -f myOtherDockerfile .
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#building-from-a-remote-dockerfile-uri)Building from a Remote Dockerfile URI

```
curl example.com/remote/Dockerfile | docker build -f - .
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-an-image)Removing an Image

```
docker image rm nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#loading-a-tarred-repository-from-a-file-or-the-standard-input-stream)Loading a Tarred Repository from a File or the Standard Input Stream

```
docker image load < ubuntu.tar.gz
```

```
docker image load --input ubuntu.tar
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#saving-an-image-to-a-tar-archive)Saving an Image to a Tar Archive

```
docker image save busybox > ubuntu.tar
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#showing-the-history-of-an-image)Showing the History of an Image

```
docker image history
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-an-image-from-a-container)Creating an Image From a Container

```
docker container commit nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#tagging-an-image)Tagging an Image

```
docker image tag nginx eon01/nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#pushing-an-image-1)Pushing an Image

```
docker image push eon01/nginx
```

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#networking)Networking

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-networks)Creating Networks

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-an-overlay-network)Creating an Overlay Network

```
docker network create -d overlay MyOverlayNetwork
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-a-bridge-network)Creating a Bridge Network

```
docker network create -d bridge MyBridgeNetwork
```

### [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-a-customized-overlay-network)Creating a Customized Overlay Network

```
docker network create -d overlay \
  --subnet=192.168.0.0/16 \
  --subnet=192.170.0.0/16 \
  --gateway=192.168.0.100 \
  --gateway=192.170.0.100 \
  --ip-range=192.168.1.0/24 \
  --aux-address="my-router=192.168.1.5" --aux-address="my-switch=192.168.1.6" \
  --aux-address="my-printer=192.170.1.5" --aux-address="my-nas=192.170.1.6" \
  MyOverlayNetwork
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-a-network)Removing a Network

```
docker network rm MyOverlayNetwork
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#listing-networks)Listing Networks

```
docker network ls
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#getting-information-about-a-network)Getting Information About a Network

```
docker network inspect MyOverlayNetwork
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#connecting-a-running-container-to-a-network)Connecting a Running Container to a Network

```
docker network connect MyOverlayNetwork nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#connecting-a-container-to-a-network-when-it-starts)Connecting a Container to a Network When it Starts

```
docker container run -it -d --network=MyOverlayNetwork nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#disconnecting-a-container-from-a-network)Disconnecting a Container from a Network

```
docker network disconnect MyOverlayNetwork nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#exposing-ports)Exposing Ports

Using Dockerfile, you can expose a port on the container using:

```
EXPOSE <port_number>
```

You can also map the container port to a host port using:

```
docker run -p $HOST_PORT:$CONTAINER_PORT --name <container_name> -t <image>
```

e.g.

```
docker run -p $HOST_PORT:$CONTAINER_PORT --name infinite -t infinite
```

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#security)Security

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#guidelines-for-building-secure-docker-images)Guidelines for building secure Docker images

1.  Prefer minimal base images
2.  Dedicated user on the image as the least privileged user
3.  Sign and verify images to mitigate MITM attacks
4.  Find, fix and monitor for open source vulnerabilities
5.  Don’t leak sensitive information to docker images
6.  Use fixed tags for immutability
7.  Use COPY instead of ADD
8.  Use labels for metadata
9.  Use multi-stage builds for small secure images
10.  Use a linter

You can find more nformation on Snyk's [10 Docker Image Security Best Practices](https://snyk.io/blog/10-docker-image-security-best-practices/) blog post.

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#cleaning-docker)Cleaning Docker

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-a-running-container)Removing a Running Container

```
docker container rm nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-a-container-and-its-volume)Removing a Container and its Volume

```
docker container rm -v nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-all-exited-containers)Removing all Exited Containers

```
docker container rm $(docker container ls -a -f status=exited -q)
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-all-stopped-containers)Removing All Stopped Containers

```
docker container rm `docker container ls -a -q`
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-a-docker-image)Removing a Docker Image

```
docker image rm nginx
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-dangling-images)Removing Dangling Images

```
docker image rm $(docker image ls -f dangling=true -q)
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-all-images)Removing all Images

```
docker image rm $(docker image ls -a -q)
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-all-untagged-images)Removing all Untagged Images

```
docker image rm -f $(docker image ls | grep "^<none>" | awk "{print $3}")
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#stopping--removing-all-containers)Stopping & Removing all Containers

```
docker container stop $(docker container ls -a -q) && docker container rm $(docker container ls -a -q)
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-dangling-volumes)Removing Dangling Volumes

```
docker volume rm $(docker volume ls -f dangling=true -q)
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#removing-all-unused-containers-images-networks-and-volumes)Removing all unused (containers, images, networks and volumes)

```
docker system prune -f
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#clean-all)Clean all

```
docker system prune -a
```

# [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#docker-swarm)Docker Swarm

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#installing-docker-swarm)Installing Docker Swarm

```
curl -ssl https://get.docker.com | bash
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#initializing-the-swarm)Initializing the Swarm

```
docker swarm init --advertise-addr 192.168.10.1
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#getting-a-worker-to-join-the-swarm)Getting a Worker to Join the Swarm

```
docker swarm join-token worker
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#getting-a-manager-to-join-the-swarm)Getting a Manager to Join the Swarm

```
docker swarm join-token manager
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#listing-services)Listing Services

```
docker service ls
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#listing-nodes)Listing nodes

```
docker node ls
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#creating-a-service)Creating a Service

```
docker service create --name vote -p 8080:80 instavote/vote
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#listing-swarm-tasks)Listing Swarm Tasks

```
docker service ps
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#scaling-a-service)Scaling a Service

```
docker service scale vote=3
```

## [](https://awesomeopensource.com/project/eon01/DockerCheatSheet#updating-a-service)Updating a Service

```
docker service update --image instavote/vote:movies vote
```

```
docker service update --force --update-parallelism 1 --update-delay 30s nginx
```

```
docker service update --update-parallelism 5--update-delay 2s --image instavote/vote:indent vote
```

```
docker service update --limit-cpu 2 nginx
```

```
docker service update --replicas=5 nginx
```

