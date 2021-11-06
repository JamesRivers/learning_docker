- rn_1605466814_portainer_docker_swarm
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-24 14:29
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://documentation.portainer.io/v2.0/deploy/ceinstallswarm/
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Deploying Portainer on a Swarm?
### Portainer Server Deployment

Use the following Docker commands to deploy the Portainer Server; note the agent is not needed on standalone hosts, however it does provide additional functionality if used:

Portainer can be directly deployed as a service in your Docker cluster. Note that this method will automatically deploy a single instance of the Portainer Server, and deploy the Portainer Agent as a global service on every node in your cluster.

```
curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
```

![](/attachments/Pasted%20image%2020210824143827.png)


```
docker stack deploy -c portainer-agent-stack.yml portainer
```

![](/attachments/Pasted%20image%2020210824143858.png)

### Portainer Agent Only Deployment

To deploy Portainer Agent on a remote Linux Swarm Cluster as a Swarm Service, run this command on a manager node in the remote cluster.

First create the network:
```
docker network create --driver overlay --attachable portainer_agent_network
```

The following step will deploy the Agent:

```
docker service create --name portainer_agent --network portainer_agent_network --publish mode=host,target=9001,published=9001 -e AGENT_CLUSTER_ADDR=tasks.portainer_agent --mode global --mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock --mount type=bind,src=//var/lib/docker/volumes,dst=/var/lib/docker/volumes --mount type=bind,src=/,dst=/host portainer/agent
```