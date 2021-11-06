- rn_1595588485_docker_delete_service_swarm
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-20 17:06
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Swarm service delete
The remaining steps in the tutorial don’t use the `helloworld` service, so now you can delete the service from the swarm.

1. If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
    
2. Run `docker service rm helloworld` to remove the `helloworld` service.

```bash
james@dock1:~$ docker service rm helloworld
helloworld
```
3. Run `docker service inspect <SERVICE-ID>` to verify that the swarm manager removed the service. The CLI returns a message that the service is not found:
```bash
james@dock1:~$ docker service inspect helloworld
[]
Status: Error: no such service: helloworld, Code: 1
```
```bash
james@dock1:~$ docker service ps helloworld
no such service: helloworld
```

4. Even though the service no longer exists, the task containers take a few seconds to clean up. You can use `docker ps` on the nodes to verify when the tasks have been removed.

```bash
james@dock1:~$ docker ps
CONTAINER ID   IMAGE           COMMAND             CREATED          STATUS          PORTS     NAMES
0ce68fe3fcfb   alpine:latest   "ping docker.com"   6 minutes ago    Up 6 minutes              helloworld.5.ldcmv7atikit4s79trv890fga
66587e3142f1   alpine:latest   "ping docker.com"   19 minutes ago   Up 19 minutes             helloworld.1.nnzslgy7kdhqlb6cnw9b6o1sf
```
