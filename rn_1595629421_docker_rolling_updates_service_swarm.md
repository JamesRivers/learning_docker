- rn_1595629421_docker_rolling_updates_service_swarm
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-20 17:17
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- [rolling updates](https://docs.docker.com/engine/swarm/swarm-tutorial/rolling-update/)
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Docker swarm service rolling updates
In a previous step of the tutorial, you [scaled](https://docs.docker.com/engine/swarm/swarm-tutorial/scale-service/) the number of instances of a service. In this part of the tutorial, you deploy a service based on the Redis 3.0.6 container tag. Then you upgrade the service to use the Redis 3.0.7 container image using rolling updates.

1.  If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
    
2.  Deploy your Redis tag to the swarm and configure the swarm with a 10 second update delay. Note that the following example shows an older Redis tag:

```bash
james@dock1:~$ docker service create \
  --replicas 3 \
  --name redis \
  --update-delay 10s \
  redis:3.0.6

mo8ji1sfbfxot1089ndlh0i4z
overall progress: 0 out of 3 tasks 
1/3: preparing [=================================>                 ] 
2/3: preparing [=================================>                 ] 
3/3: preparing [=================================>                 ] 
```
You configure the rolling update policy at service deployment time.

The `--update-delay` flag configures the time delay between updates to a service task or sets of tasks. You can describe the time `T` as a combination of the number of seconds `Ts`, minutes `Tm`, or hours `Th`. So `10m30s` indicates a 10 minute 30 second delay.

By default the scheduler updates 1 task at a time. You can pass the `--update-parallelism` flag to configure the maximum number of service tasks that the scheduler updates simultaneously.

By default, when an update to an individual task returns a state of `RUNNING`, the scheduler schedules another task to update until all tasks are updated. If, at any time during an update a task returns `FAILED`, the scheduler pauses the update. You can control the behavior using the `--update-failure-action` flag for `docker service create` or `docker service update`.

3. Inspect the redis service

```bash
james@dock1:~$ docker service inspect --pretty redis

ID:		mo8ji1sfbfxot1089ndlh0i4z
Name:		redis
Service Mode:	Replicated
 Replicas:	3
Placement:
UpdateConfig:
 Parallelism:	1
 Delay:		10s
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
 Image:		redis:3.0.6@sha256:6a692a76c2081888b589e26e6ec835743119fe453d67ecf03df7de5b73d69842
 Init:		false
Resources:
Endpoint Mode:	vip
```
4. Now you can update the container image for `redis`. The swarm manager applies the update to nodes according to the `UpdateConfig` policy:

```bash
james@dock1:~$ docker service update --image redis:3.0.7 redis
redis
overall progress: 0 out of 3 tasks 
1/3: preparing [=================================>                 ] 
2/3:   
3/3:   
```
The scheduler applies rolling updates as follows by default:
    
    -   Stop the first task.
    -   Schedule update for the stopped task.
    -   Start the container for the updated task.
    -   If the update to a task returns `RUNNING`, wait for the specified delay period then start the next task.
    -   If, at any time during the update, a task returns `FAILED`, pause the update.
5. Run `docker service inspect --pretty redis` to see the new image in the desired state:

```bash
james@dock1:~$ docker service inspect --pretty redis

ID:		mo8ji1sfbfxot1089ndlh0i4z
Name:		redis
Service Mode:	Replicated
 Replicas:	3
UpdateStatus:
 State:		completed
 Started:	About a minute ago
 Completed:	2 seconds ago
 Message:	update completed
Placement:
UpdateConfig:
 Parallelism:	1
 Delay:		10s
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
 Image:		redis:3.0.7@sha256:730b765df9fe96af414da64a2b67f3a5f70b8fd13a31e5096fee4807ed802e20
 Init:		false
Resources:
Endpoint Mode:	vip
```
The output of `service inspect` could show if your update paused due to failure.

To restart a paused update run `docker service update <SERVICE-ID>`. For example.

To avoid repeating certain update failures, you may need to reconfigure the service by passing flags to `docker service update`.

Run `docker service ps <SERVICE-ID>` to watch the rolling update:
```bash
james@dock1:~$ docker service ps redis
ID             NAME          IMAGE         NODE      DESIRED STATE   CURRENT STATE            ERROR                              PORTS
r9ssb839sial   redis.1       redis:3.0.7   dock2     Running         Running 3 minutes ago                                       
o0lxcb14ztc8    \_ redis.1   redis:3.0.6   dock2     Shutdown        Failed 3 minutes ago     "cannot stop container: redis.…"   
m67bc9xj2xys   redis.2       redis:3.0.7   dock3     Running         Running 2 minutes ago                                       
6qkzjook1yf2    \_ redis.2   redis:3.0.6   dock3     Shutdown        Shutdown 2 minutes ago                                      
qoax6cjko5sh   redis.3       redis:3.0.7   dock1     Running         Running 2 minutes ago                                       
pp0o9rqw16vt    \_ redis.3   redis:3.0.6   dock1     Shutdown        Failed 3 minutes ago     "cannot stop container: redis.…"   
```

Before Swarm updates all of the tasks, you can see that some are running `redis:3.0.6` while others are running `redis:3.0.7`. The output above shows the state once the rolling updates are done.


