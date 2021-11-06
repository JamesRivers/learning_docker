Daily Docker Notes - what i find and issues I encounter. 

# Docker Networks 
learn it and review all the differing types. 



# Install issue diary
### 210824
found an issue today - I am able to run up the docker compose for `hermes` on node 1 as a single no problem. But I am not able to deploy via a docker swarm.  
Troubleshooting steps
- I should run a test swarm deploy on something other than imagine. Review my deploy a service  notes... [rn_1595501730_docker_deploy_service_swarm](rn_1595501730_docker_deploy_service_swarm.md)
- `docker service create --replicas 1 --name helloworld alpine ping docker.com`
	- result - PASS 
![](/attachments/Pasted%20image%2020210825114613.png)
This creates a service and deploys across the nodes. It does not create a stack. 

### 211028 Udemy course progress
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

## 21/10/29
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












	
