- rn_1595501730_docker_deploy_service_swarm
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-20 16:47
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- [deploy services](https://docs.docker.com/engine/swarm/swarm-tutorial/deploy-service/)
		- [inspect services](https://docs.docker.com/engine/swarm/swarm-tutorial/inspect-service/)
		- [scale services](https://docs.docker.com/engine/swarm/swarm-tutorial/scale-service/)
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Swarm service deploy
After you [create a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/), you can deploy a service to the swarm. For this tutorial, you also [added worker nodes](https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/), but that is not a requirement to deploy a service.

1.  Open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
    
2.  Run the following command:

```bash
james@dock1:~$ docker service create --replicas 1 --name helloworld alpine ping docker.com
82tu3wrt2alfxcuro0n4vzizh
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 
```
-   The `docker service create` command creates the service.
-   The `--name` flag names the service `helloworld`.
-   The `--replicas` flag specifies the desired state of 1 running instance.
-   The arguments `alpine ping docker.com` define the service as an Alpine Linux container that executes the command `ping docker.com`.

3. Run `docker service ls` to see the list of running services:
```bash
james@dock1:~$ docker service ls
ID             NAME         MODE         REPLICAS   IMAGE           PORTS
82tu3wrt2alf   helloworld   replicated   1/1        alpine:latest   
```

## Inspect a service on the swarm

When you have [deployed a service](https://docs.docker.com/engine/swarm/swarm-tutorial/deploy-service/) to your swarm, you can use the Docker CLI to see details about the service running in the swarm.

1.  If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
    
2.  Run `docker service inspect --pretty <SERVICE-ID>` to display the details about a service in an easily readable format. To see the details on the `helloworld` service:
```bash
james@dock1:~$ docker service inspect --pretty helloworld

ID:		82tu3wrt2alfxcuro0n4vzizh
Name:		helloworld
Service Mode:	Replicated
 Replicas:	1
Placement:
UpdateConfig:
 Parallelism:	1
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Update order:      stop-first
RollbackConfig:
 Parallelism:	1
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Rollback order:    stop-first
ContainerSpec:
 Image:		alpine:latest@sha256:eb3e4e175ba6d212ba1d6e04fc0782916c08e1c9d7b45892e9796141b1d379ae
 Args:		ping docker.com 
 Init:		false
Resources:
Endpoint Mode:	vip
```
> **Tip**: To return the service details in json format, run the same command without the `--pretty` flag.
```bash
james@dock1:~$ docker service inspect helloworld
[
    {
        "ID": "82tu3wrt2alfxcuro0n4vzizh",
        "Version": {
            "Index": 21
        },
        "CreatedAt": "2021-08-20T15:48:57.815634699Z",
        "UpdatedAt": "2021-08-20T15:48:57.815634699Z",
        "Spec": {
            "Name": "helloworld",
            "Labels": {},
            "TaskTemplate": {
                "ContainerSpec": {
                    "Image": "alpine:latest@sha256:eb3e4e175ba6d212ba1d6e04fc0782916c08e1c9d7b45892e9796141b1d379ae",
                    "Args": [
                        "ping",
                        "docker.com"
                    ],
                    "Init": false,
                    "StopGracePeriod": 10000000000,
                    "DNSConfig": {},
                    "Isolation": "default"
                },
                "Resources": {
                    "Limits": {},
                    "Reservations": {}
--- SNIP
```

> **Tip**: If you forget the commands for the docker cli - then add a `?` to the cli and see what is the next possible entry... Helps me. 
```bash
james@dock1:~$ docker service ?

Usage:  docker service COMMAND

Manage services

Commands:
  create      Create a new service
  inspect     Display detailed information on one or more services
  logs        Fetch the logs of a service or task
  ls          List services
  ps          List the tasks of one or more services
  rm          Remove one or more services
  rollback    Revert changes to a service's configuration
  scale       Scale one or multiple replicated services
  update      Update a service
```
3. Run `docker service ps <SERVICE-ID>` to see which nodes are running the service:
```bash
james@dock1:~$ docker service ps helloworld
ID             NAME           IMAGE           NODE      DESIRED STATE   CURRENT STATE           ERROR     PORTS
nnzslgy7kdhq   helloworld.1   alpine:latest   dock1     Running         Running 6 minutes ago             
```

In this case, the one instance of the `helloworld` service is running on the `dock1` node. This is my manager node and not the worker nodes... You may see the service running on your manager node. By default, manager nodes in a swarm can execute tasks just like worker nodes.

Swarm also shows you the `DESIRED STATE` and `CURRENT STATE` of the service task so you can see if tasks are running according to the service definition.

4. Run `docker ps` on the node where the task is running to see details about the container for the task.

> **Tip**: If `helloworld` is running on a node other than your manager node, you must ssh to that node.

```bash
james@dock1:~$ docker ps
CONTAINER ID   IMAGE           COMMAND             CREATED         STATUS         PORTS     NAMES
66587e3142f1   alpine:latest   "ping docker.com"   9 minutes ago   Up 9 minutes             helloworld.1.nnzslgy7kdhqlb6cnw9b6o1sf
```

# Scale the service
Once you have [deployed a service](https://docs.docker.com/engine/swarm/swarm-tutorial/deploy-service/) to a swarm, you are ready to use the Docker CLI to scale the number of containers in the service. Containers running in a service are called “tasks.”

1.  If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
    
2.  Run the following command to change the desired state of the service running in the swarm:
```bash
docker service scale <SERVICE-ID>=<NUMBER-OF-TASKS>
```

This would something like -
```bash
james@dock1:~$ docker service scale helloworld=5
helloworld scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converged 
```

3. Run `docker service ps <SERVICE-ID>` to see the updated task list:

```bash
james@dock1:~$ docker service ps helloworld
ID             NAME           IMAGE           NODE      DESIRED STATE   CURRENT STATE            ERROR     PORTS
nnzslgy7kdhq   helloworld.1   alpine:latest   dock1     Running         Running 13 minutes ago             
zlp0f5dbegom   helloworld.2   alpine:latest   dock2     Running         Running 49 seconds ago             
rb6lhxm82e0e   helloworld.3   alpine:latest   dock3     Running         Running 50 seconds ago             
tzi6opyfno09   helloworld.4   alpine:latest   dock3     Running         Running 47 seconds ago             
ldcmv7atikit   helloworld.5   alpine:latest   dock1     Running         Running 53 seconds ago     
```
You can see that swarm has created 4 new tasks to scale to a total of 5 running instances of Alpine Linux. The tasks are distributed between the three nodes of the swarm. 2 are is running on `manager1`.

Run `docker ps` to see the containers running on the node where you’re connected. The following example shows the tasks running on `manager1`:

```bash
james@dock1:~$ docker ps
CONTAINER ID   IMAGE           COMMAND             CREATED          STATUS          PORTS     NAMES
0ce68fe3fcfb   alpine:latest   "ping docker.com"   2 minutes ago    Up 2 minutes              helloworld.5.ldcmv7atikit4s79trv890fga
66587e3142f1   alpine:latest   "ping docker.com"   15 minutes ago   Up 15 minutes             helloworld.1.nnzslgy7kdhqlb6cnw9b6o1sf
```
If you want to see the containers running on other nodes, ssh into those nodes and run the `docker ps` command.

## Exercise 
- swarm app 1
- Dockers distributed voting app
- create 1 volume, 2 networks, 5 services 
- create the commands as needed and spin up the services
- test the application 
- note the exercise is using docker hub images

![](/attachments/Pasted%20image%2020211028144030.png)

Good exercise to have folks run through. 

Create an application for voting. Taking the docker samples from https://github.com/dockersamples/example-voting-app - you need to create the following:

![](/attachments/Pasted%20image%2020211028174341.png)

Here is the challenge... 

### Assignment: Create A Multi-Service Multi-Node Web App

#### Goal: create networks, volumes, and services for a web-based "cats vs. dogs" voting app.

You can perform all of this with - https://labs.play-with-docker.com when you need to open the web ui, click on open port and enter the ports you need.

![](/attachments/Pasted%20image%2020211028174537.png)

---

#### Key items for you:
- All images are on Docker Hub. 
- a `backend` and `frontend` overlay network are needed. Nothing different about them other than that backend will help protect database from the voting web app. (similar to how a VLAN setup might be in traditional architecture)
- The database server should use a named volume for preserving data. Use the new `--mount` format to do this: `--mount type=volume,source=db-data,target=/var/lib/postgresql/data`
- you are going to be using the `docker service` commands here, not the `docker stack`

Good luck and have some fun with it!! I will post up answers in a few days time. 

#### Services (names below should be service names)

- vote
    - dockersamples/examplevotingapp_vote:before
    - web front end for users to vote dog/cat
    - ideally published on TCP 80. Container listens on 80
    - on frontend network
    - 2+ replicas of this container

- worker
    - dockersamples/examplevotingapp_worker
    - backend processor of redis and storing results in postgres
    - no public ports
    - on frontend and backend networks
    - 1 replica
- db
    - postgres:9.4
    - one named volume needed, pointing to /var/lib/postgresql/data
    - on backend network
    - 1 replica
    - remember set env for password-less connections -e POSTGRES_HOST_AUTH_METHOD=trust
- result
    - dockersamples/examplevotingapp_result:before
    - web app that shows results
    - runs on high port since just for admins (lets imagine)
    - so run on a high port of your choosing (I choose 5001), container listens on 80
    - on backend network
    - 1 replica

--- 
> The RWX Method, Refining the Scientific Method ... 
> 1.  Read, Research, Reach Out, Repeat
> 2.  Write Who, What (If), When, Where, Why, and How
> 3.  Explore, Experiment, and Exercise
>     1.  Fail Faster
----
### Answers 
- node 1
	- `docker swarm init`
		- or `docker swarm init --advertise-addr a.b.c.d`
	- `docker node ls`
	- copy the swarm token 
- node 2/3
	- paste the docker swarm token join command
	- `docker swarm join --token yourtoken yourIP:port` 
- node 1 
	- network create 
		- `docker network create -d overlay backend`
		- `docker network create -d overlay frontend`
		- `docker network ls`
	- vote service
		- `docker service create --name vote -p 80:80 --network frontend --replicas 2 dockersamples/examplevotingapp_vote:before`
		- check with `docker service ls`
	- redis service
		- `docker service create --name redis --network frontend --replicas 1 redis:3.2`
	- db
		- `docker service create --name db --network backend --mount type=volume,source=db-data,target=/var/lib/postgresql/data -e POSTGRES_HOST_AUTH_METHOD=trust postgres:9.4`
	- worker service 
		- `docker service create --name worker --network frontend --network backend --replicas 1 dockersamples/examplevotingapp_worker`
	- result service 
		- `docker service create --name result --network backend -p 5001:80 --replicas 1 dockersamples/examplevotingapp_result:before`
	- check with `docker serivce ls`

```bash
$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                                          PORTS
xeubyovc05n6   db        replicated   1/1        postgres:9.4                                   
6uc3x2enhawt   redis     replicated   1/1        redis:3.2                                      
7hltrhxjyix5   result    replicated   1/1        dockersamples/examplevotingapp_result:before   *:5001->80/tcp
i4p2hi6w72ba   vote      replicated   2/2        dockersamples/examplevotingapp_vote:before     *:80->80/tcp
yhk1cflobkx2   worker    replicated   1/1        dockersamples/examplevotingapp_worker:latest   
```
- check with `docker serivce ps name`

```bash
$ docker service ps vote
ID             NAME      IMAGE                                        NODE      DESIRED STATE   CURRENT STATE            ERROR     PORTS
csbm4916x8fm   vote.1    dockersamples/examplevotingapp_vote:before   node2     Running         Running 17 minutes ago             
xeuxyeqxtniw   vote.2    dockersamples/examplevotingapp_vote:before   node1     Running         Running 17 minutes ago 
```
- check service logs `docker service logs name`
```bash
$ docker service logs vote 
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [1] [INFO] Starting gunicorn 19.6.0
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [1] [INFO] Using worker: sync
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [11] [INFO] Booting worker with pid: 11
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [12] [INFO] Booting worker with pid: 12
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [13] [INFO] Booting worker with pid: 13
vote.2.xeuxyeqxtniw@node1    | [2021-10-28 17:26:34 +0000] [14] [INFO] Booting worker with pid: 14
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:34 +0000] [1] [INFO] Starting gunicorn 19.6.0
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:34 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:34 +0000] [1] [INFO] Using worker: sync
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:34 +0000] [11] [INFO] Booting worker with pid: 11
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:34 +0000] [12] [INFO] Booting worker with pid: 12
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:35 +0000] [18] [INFO] Booting worker with pid: 18
vote.1.csbm4916x8fm@node2    | [2021-10-28 17:26:35 +0000] [22] [INFO] Booting worker with pid: 22
```

### Start Voting
![](/attachments/2021-10-28%2018.49.10.gif)
![](/attachments/2021-10-28%2018.50.01.gif)

