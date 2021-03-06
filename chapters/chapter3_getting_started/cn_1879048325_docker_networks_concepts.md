- cn_1879048325_docker_networks_concepts
	- Created on the: 2021-11-11 12:49
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- Brett Fisher
		- https://docs.docker.com/engine/tutorials/networkingcontainers/
		- https://docs.docker.com/engine/reference/commandline/inspect/
	- Author Notes: 
	- Tags: #docker 
	- ICON set : 
		- Warning โ ๏ธ - Important need to know / Notes ๐ - Additional notes / Version ๐ฑ - Specific to a Version / Knowledge ๐ง  - Did you know / WWW ๐ธ - Links to web pages / Learning AIM ๐ฏ - The Learning target for this topic / Evaluation ๐งช - Set of evaluation items to show the target was reached / Exercise ๐คธ - Hands on time... /  Reading ๐  - Items for you to read / Requirements โ before you start you need to know.
---
# docker networks
Requirements โ - know how to start a container, plus under the TCP/IP stack, subnets, firewalls etc... 
Learning AIM ๐ฏ 
- cover docker ports
- docker port checking 
- concepts of docker networks  and docker networking 
- docker network diagram overview

## docker network defaults

- Each container connected to a private virtual network "bridge"
- Each virtual network routes through NAT firewall on host IP
- All containers on a virtual network can talk to each other without -p
- Best practice is to create a new virtual network for each app: 
	- network "my_web_app" or mysql and php/apache container
	- network "my_api" for mongo and nodejs containers

- "Batteries Included, But Removable"
	- Defaults work well in many cases, but easy to swap out parts to customize it
-Make new virtual networks
- Attach containers to more then one virtual network (or none) 
- Skip virtual networks and use host IP (--net=host)
	- [docs.docker --net](https://docs.docker.com/engine/tutorials/networkingcontainers/)
	-  Use different Docker network drivers to gain new abilities
		-  and much more...

Exercise ๐คธ - start a new container and will pay special attention to the `--publish` command.  `docker container run -p 80:80 --name webserver -d nginx`

Notes ๐  always remeber the publishing command is HOST PORT : CONTAINER PORT format. 

Use a new command `docker container port`  this will show the container port mapping and sources. 
```bash
โ docker container port de
80/tcp -> 0.0.0.0:80
```

What is the IP address of the container? The same as the host IP address? Yes and no. Docker has assigned a virtual IP to the container that we can see via the `docker container inspect`  lets check it out...

I know that the detail I am looking for is under `NetworkSettings` so I am going to use the `--format` option.  Note `de` is the ID of the container
```bash
โฏ docker container inspect --format '{{ .NetworkSettings.IPAddress }}' de
172.17.0.2
``` 
there we go the docker virtual ip for this container is `172.17.0.2`

In this example we need to understand that container as can talk to other containers if they are on the same network. Here we can see that mysql and the container connected to the `mynet` network can talk to each other.

![](../../attachments/Pasted%20image%2020211111132351.png)






