- rn_1609115804_docker_stack_deploy
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-25 11:54
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# deploy docker swarm stack
Deploy a new stack or update an existing stack

[API 1.25+](https://docs.docker.com/engine/api/v1.25/)  The client and daemon API must both be at least [1.25](https://docs.docker.com/engine/api/v1.25/) to use this command. Use the `docker version` command on the client to check your client and daemon API versions.

> Notes 🗒 This is a cluster management command, and must be executed on a swarm manager node. To learn about managers and workers, refer to the [Swarm mode section](https://docs.docker.com/engine/swarm/) in the documentation.


## Options[](https://docs.docker.com/engine/reference/commandline/stack_deploy/#options)

- `--compose-file` , `-c`
	- [API 1.25+](https://docs.docker.com/engine/api/v1.25/)  
	- Path to a Compose file, or "-" to read from stdin

- `--namespace`
	- [deprecated](https://docs.docker.com/engine/deprecated/)
	- Kubernetes namespace to use
- `--prune`
	- [API 1.27+](https://docs.docker.com/engine/api/v1.27/)
	- Swarm Prune services that are no longer referenced
- `--resolve-image` 
	- default - `always`
	- [API 1.30+](https://docs.docker.com/engine/api/v1.30/) 
	- Swarm Query the registry to resolve image digest and supported platforms ("always"|"changed"|"never")
- `--with-registry-auth`
	- Swarm Send registry authentication details to Swarm agents 
- `--kubeconfig`
	- [deprecated](https://docs.docker.com/engine/deprecated/)
	- Kubernetes config file
- `--orchestrator`
	- [deprecated](https://docs.docker.com/engine/deprecated/)  
	- Orchestrator to use (swarm|kubernetes|all)

> Warning ⚠️ The `deploy` command supports compose file version `3.0` and above.

## Examples
```
docker stack deploy --compose-file docker-compose.yml vossibility

Ignoring unsupported options: links

Creating network vossibility_vossibility
Creating network vossibility_default
Creating service vossibility_nsqd
Creating service vossibility_logstash
Creating service vossibility_elasticsearch
Creating service vossibility_kibana
Creating service vossibility_ghollector
Creating service vossibility_lookupd
```

The Compose file can also be provided as standard input with `--compose-file -`:

```
$ cat docker-compose.yml | docker stack deploy --compose-file - vossibility

Ignoring unsupported options: links

Creating network vossibility_vossibility
Creating network vossibility_default
Creating service vossibility_nsqd
Creating service vossibility_logstash
Creating service vossibility_elasticsearch
Creating service vossibility_kibana
Creating service vossibility_ghollector
Creating service vossibility_lookupd
```

If your configuration is split between multiple Compose files, e.g. a base configuration and environment-specific overrides, you can provide multiple `--compose-file` flags.

```
$ docker stack deploy --compose-file docker-compose.yml -c docker-compose.prod.yml vossibility

Ignoring unsupported options: links

Creating network vossibility_vossibility
Creating network vossibility_default
Creating service vossibility_nsqd
Creating service vossibility_logstash
Creating service vossibility_elasticsearch
Creating service vossibility_kibana
Creating service vossibility_ghollector
Creating service vossibility_lookupd
```

ou can verify that the services were correctly created:

```
$ docker service ls

ID            NAME                               MODE        REPLICAS  IMAGE
29bv0vnlm903  vossibility_lookupd                replicated  1/1       nsqio/nsq@sha256:eeba05599f31eba418e96e71e0984c3dc96963ceb66924dd37a47bf7ce18a662
4awt47624qwh  vossibility_nsqd                   replicated  1/1       nsqio/nsq@sha256:eeba05599f31eba418e96e71e0984c3dc96963ceb66924dd37a47bf7ce18a662
4tjx9biia6fs  vossibility_elasticsearch          replicated  1/1       elasticsearch@sha256:12ac7c6af55d001f71800b83ba91a04f716e58d82e748fa6e5a7359eed2301aa
7563uuzr9eys  vossibility_kibana                 replicated  1/1       kibana@sha256:6995a2d25709a62694a937b8a529ff36da92ebee74bafd7bf00e6caf6db2eb03
9gc5m4met4he  vossibility_logstash               replicated  1/1       logstash@sha256:2dc8bddd1bb4a5a34e8ebaf73749f6413c101b2edef6617f2f7713926d2141fe
axqh55ipl40h  vossibility_vossibility-collector  replicated  1/1       icecrime/vossibility-collector@sha256:f03f2977203ba6253988c18d04061c5ec7aab46bca9dfd89a9a1fa4500989fba
```

## Parent command[](https://docs.docker.com/engine/reference/commandline/stack_deploy/#parent-command)

[docker stack](https://docs.docker.com/engine/reference/commandline/stack/)

Manage Docker stacks

## Related commands[](https://docs.docker.com/engine/reference/commandline/stack_deploy/#related-commands)

[docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/)

- Deploy a new stack or update an existing stack

[docker stack ls](https://docs.docker.com/engine/reference/commandline/stack_ls/)

- List stacks

[docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/)

- List the tasks in the stack

[docker stack rm](https://docs.docker.com/engine/reference/commandline/stack_rm/)

- Remove one or more stacks

[docker stack services](https://docs.docker.com/engine/reference/commandline/stack_services/)

- List the services in the stack

## Exercise 
Docker stack deploy 
![](/attachments/Pasted%20image%2020211029114958.png)

[rn_1609115804_docker_stack_deploy](rn_1609115804_docker_stack_deploy.md)

Exercise number 2 for this class using docker stack deploy... the new better way to get this going. 

`Deploy` is a key word 

in this exercise use the docker stack deploy example located at -  https://github.com/dockersamples/example-voting-app/blob/master/docker-stack-simple.yml

review the yaml file and reference the diffing items that er may not have seen before - such as `deploy` 

to deploy the stack you need to run the command - `docker stack deploy -c yourfile.yml votingapp`
```bash
$ docker stack deploy -c dockerstack.yml votingapp
Creating network votingapp_frontend
Creating network votingapp_backend
Creating service votingapp_redis
Creating service votingapp_db
Creating service votingapp_vote
Creating service votingapp_result
Creating service votingapp_worker
```

All it did was create those objects in the scheduler, which will then go through the process of creating the services, which then create the tasks, which then create the containers.

It also has to created the networks as you'll see here. Remember we had the frontend and the backend so it created those, as well as a default network, which that particular stack file that I had was using a default network.


`docker stack ls `
```bash 
$ docker stack ls
NAME        SERVICES   ORCHESTRATOR
votingapp   5          Swarm
```
```bash 
$ docker service ls
ID             NAME               MODE         REPLICAS   IMAGE                                          PORTS
u8r8uwf6fs01   votingapp_db       replicated   1/1        postgres:9.4                                   
9jy70mal43m9   votingapp_redis    replicated   1/1        redis:alpine                                   *:30000->6379/tcp
z49bvpkd0x6m   votingapp_result   replicated   1/1        dockersamples/examplevotingapp_result:before   *:5001->80/tcp
ty1o2a14drls   votingapp_vote     replicated   1/1        dockersamples/examplevotingapp_vote:before     *:5000->80/tcp
scscstm085q3   votingapp_worker   replicated   1/1        dockersamples/examplevotingapp_worker:latest   
```
```bash
$ docker network ls
NETWORK ID     NAME                 DRIVER    SCOPE
a8e26629e5df   bridge               bridge    local
4ee0bb4ee283   docker_gwbridge      bridge    local
b542e85c32fd   host                 host      local
xr5ymzom0j4a   ingress              overlay   swarm
69a1ecb943cb   none                 null      local
go3uzyba5sd7   votingapp_backend    overlay   swarm
h2fr0z001gmz   votingapp_frontend   overlay   swarm
```
new command - `docker stack ps`
```bash
$ docker stack ps votingapp 
ID             NAME                 IMAGE                                          NODE      DESIRED STATE   CURRENT STATE           ERROR     PORTS
ewfqmbotcelo   votingapp_db.1       postgres:9.4                                   node1     Running         Running 5 minutes ago             
nn3qcvpcmik6   votingapp_redis.1    redis:alpine                                   node1     Running         Running 5 minutes ago             
ugid2yi9k7hw   votingapp_result.1   dockersamples/examplevotingapp_result:before   node3     Running         Running 5 minutes ago             
3clk6n6zqoby   votingapp_vote.1     dockersamples/examplevotingapp_vote:before     node2     Running         Running 6 minutes ago             
a9wp6v1fb5hi   votingapp_worker.1   dockersamples/examplevotingapp_worker:latest   node1     Running         Running 5 minutes ago  
```
These are the tasks not the containers. 

another command - `docker stack services name`
```bash
$ docker stack services votingapp 
ID             NAME               MODE         REPLICAS   IMAGE                                          PORTS
u8r8uwf6fs01   votingapp_db       replicated   1/1        postgres:9.4                                   
9jy70mal43m9   votingapp_redis    replicated   1/1        redis:alpine                                   *:30000->6379/tcp
z49bvpkd0x6m   votingapp_result   replicated   1/1        dockersamples/examplevotingapp_result:before   *:5001->80/tcp
ty1o2a14drls   votingapp_vote     replicated   1/1        dockersamples/examplevotingapp_vote:before     *:5000->80/tcp
scscstm085q3   votingapp_worker   replicated   1/1        dockersamples/examplevotingapp_worker:latest   
```
The stack name always proceeds the object- either a network or a service etc.. 

Test - remove the stack add in a new item to the compose file - the `visualizer`...
https://hub.docker.com/r/dockersamples/visualizer

> This project was originally created by Francisco Miranda for the 2015 DockerCon EU keynote. It was adapted to be used for the 2016 DockerCon US keynote showcasing Docker swarm mode. Since then the community has generously contributed many updates. Thanks to all the contributors, and a special thanks to @DovAmir and @alexellis for their big contributions.
> 
> Demo container that displays Docker services running on a Docker Swarm in a diagram.
> This works only with Docker swarm mode which was introduced in Docker 1.12. These instructions presume you are running on the master node and you already have a Swarm running.
> 
> Each node in the swarm will show all tasks running on it. When a service goes down it'll be removed. When a node goes down it won't, instead the circle at the top will turn red to indicate it went down. Tasks will be removed. Occasionally the Remote API will return incomplete data, for instance the node can be missing a name. The next time info for that node is pulled, the name will update.

```bash
visualizer:
      image: dockersamples/visualizer
      ports:
        - 8080:8080
      stop_grace_period: 1m30s
      networks:
        - frontend
      volumes:
        - /var/run/docker.sock:/var/run/docker.sock
      deploy:
        placement:
          constraints: [node.role == manager]
 ```
add before the `networks` section of you yml. 

New service created... check it out. 

![](/attachments/Pasted%20image%2020211029121711.png)

Check out the different colors around the services... 

Update the the number replicas for a given service - the voter service how are you going to do it? 
- docker service scale? 
- or edit the yml file? 

Recommend you edit the yml, otherwise you could get into a state by where you updated replicas by a docker service cmd, then revert back when a docker stack is redeployed, bit of an anti pattern approach....

So edit the yml file and change the number of replicas to a larger number. 

then run the same cmd you ran earlier `docker stack deploy -c yourfile.yml votingapp`  
> be careful and make sure you use the same name... 

```bash 
$ docker stack deploy -c dockerstack.yml votingapp
Updating service votingapp_result (id: k28gu4udmxondxu7i2xtuizii)
Updating service votingapp_worker (id: 7212n5xncsh7g7pefe0dny389)
Updating service votingapp_visualizer (id: xorq3x26fgqtdwyxwwtfz1l3v)
Updating service votingapp_redis (id: e73pv43pk5alwzvdcjol0ri5a)
Updating service votingapp_db (id: vs1uuyo9blcg1bbveizqskopv)
Updating service votingapp_vote (id: zefc3dfi75ehuqb7px919zlq8)
```
notice the `updating service output`. 

now on the visualizer we should see more services. 
![](/attachments/Pasted%20image%2020211029122413.png)