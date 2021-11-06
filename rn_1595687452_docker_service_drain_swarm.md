- rn_1595687452_docker_service_drain_swarm
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-20 17:30
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- [drain nodes](https://docs.docker.com/engine/swarm/swarm-tutorial/drain-node/)
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Docker swarm drain nodes
In earlier steps of the tutorial, all the nodes have been running with `ACTIVE` availability. The swarm manager can assign tasks to any `ACTIVE` node, so up to now all nodes have been available to receive tasks.

Sometimes, such as planned maintenance times, you need to set a node to `DRAIN` availability. `DRAIN` availability prevents a node from receiving new tasks from the swarm manager. It also means the manager stops tasks running on the node and launches replica tasks on a node with `ACTIVE` availability.

> ⚠️**Important**🧠: Setting a node to `DRAIN` does not remove standalone containers from that node, such as those created with `docker run`, `docker-compose up`, or the Docker Engine API. A node’s status, including `DRAIN`, only affects the node’s ability to schedule swarm service workloads.

1. If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
    
2.  Verify that all your nodes are actively available.

```bash
james@dock1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
shrahbxphxqyu8t4acb2ples4 *   dock1      Ready     Active         Leader           19.03.13
jfe1aktaf71u8yw171mh51en2     dock2      Ready     Active                          19.03.13
nm7jt9j73ndmz55b1izmmzdy4     dock3      Ready     Active                          20.10.8
```

3. If you aren’t still running the `redis` service from the [rolling update](https://docs.docker.com/engine/swarm/swarm-tutorial/rolling-update/) tutorial, start it now:

```bash
docker service create --replicas 3 --name redis --update-delay 10s redis:3.0.6
```

4. Run `docker service ps redis` to see how the swarm manager assigned the tasks to different nodes:

```bash
james@dock1:~$ docker service ps redis
ID             NAME          IMAGE         NODE      DESIRED STATE   CURRENT STATE            ERROR                              PORTS
r9ssb839sial   redis.1       redis:3.0.7   dock2     Running         Running 10 minutes ago                                      
o0lxcb14ztc8    \_ redis.1   redis:3.0.6   dock2     Shutdown        Failed 10 minutes ago    "cannot stop container: redis.…"   
m67bc9xj2xys   redis.2       redis:3.0.7   dock3     Running         Running 9 minutes ago                                       
6qkzjook1yf2    \_ redis.2   redis:3.0.6   dock3     Shutdown        Shutdown 9 minutes ago                                      
qoax6cjko5sh   redis.3       redis:3.0.7   dock1     Running         Running 9 minutes ago                                       
pp0o9rqw16vt    \_ redis.3   redis:3.0.6   dock1     Shutdown        Failed 10 minutes ago    "cannot stop container: redis.…"   
```
In this case the swarm manager distributed one task to each node. You may see the tasks distributed differently among the nodes in your environment.

> NOTE - The containers for those services are removed after a rolling update; you are simply left with a log of those that were shutdown. You can limit the number you see using

```bash
docker swarm update --task-history-limit 5
```
> Docker keeps several copies of past services, but deletes them as you update. So, if you're history is full, the oldest copy will be deleted immediately and the most recently deleted copy will be added to your history.

5. Run `docker node update --availability drain <NODE-ID>` to drain a node that had a task assigned to it:
```bash
james@dock1:~$ docker node update --availability drain dock2
dock2
```

6. Inspect the node to check its availability:

```bash
james@dock1:~$ docker node inspect --pretty dock2
ID:			jfe1aktaf71u8yw171mh51en2
Hostname:              	dock2
Joined at:             	2021-08-20 15:32:23.754682853 +0000 utc
Status:
 State:			Ready
 Availability:         	Drain
 Address:		192.168.1.170
Platform:
 Operating System:	linux
 Architecture:		x86_64
Resources:
 CPUs:			4
 Memory:		3.835GiB
Plugins:
 Log:		awslogs, fluentd, gcplogs, gelf, journald, json-file, local, logentries, splunk, syslog
 Network:		bridge, host, ipvlan, macvlan, null, overlay
 Volume:		local
Engine Version:		19.03.13
TLS Info:
 TrustRoot:
-----BEGIN CERTIFICATE-----
MIIBajCCARCgAwIBAgIUEYcl+HxbZ02qJz3zzOc4zvgV32QwCgYIKoZIzj0EAwIw
EzERMA8GA1UEAxMIc3dhcm0tY2EwHhcNMjEwODIwMTUyMjAwWhcNNDEwODE1MTUy
MjAwWjATMREwDwYDVQQDEwhzd2FybS1jYTBZMBMGByqGSM49AgEGCCqGSM49AwEH
A0IABMkA2uggLcg+XfQLKgmvErSTfH2QWrWtHxmRwQ5WrchkfgHVPagqcuvd45o4
YMT+AIHrDKBiX9vecN8dBgVE4sGjQjBAMA4GA1UdDwEB/wQEAwIBBjAPBgNVHRMB
Af8EBTADAQH/MB0GA1UdDgQWBBSSKq5xC4Swy7iUduE6Gr3riirGjTAKBggqhkjO
PQQDAgNIADBFAiEAxqD78onxWxfMmXvLxUaGgiwGb5TJq648ZTdR4ekng4ICIDAp
4QzOI8FbUF9WbJ6bHjFFBlnxbXlWI6wqdJ4AAWgN
-----END CERTIFICATE-----

 Issuer Subject:	MBMxETAPBgNVBAMTCHN3YXJtLWNh
 Issuer Public Key:	MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEyQDa6CAtyD5d9AsqCa8StJN8fZBata0fGZHBDlatyGR+AdU9qCpy693jmjhgxP4AgesMoGJf295w3x0GBUTiwQ==
```
The drained node shows `Drain` for `AVAILABILITY`.

7. Run `docker service ps redis` to see how the swarm manager updated the task assignments for the `redis` service:

```bash
james@dock1:~$ docker service ps redis
ID             NAME          IMAGE         NODE      DESIRED STATE   CURRENT STATE                ERROR                              PORTS
80thq1n9nko8   redis.1       redis:3.0.7   dock1     Running         Running about a minute ago                                      
r9ssb839sial    \_ redis.1   redis:3.0.7   dock2     Shutdown        Failed about a minute ago    "cannot stop container: redis.…"   
o0lxcb14ztc8    \_ redis.1   redis:3.0.6   dock2     Shutdown        Failed 18 minutes ago        "cannot stop container: redis.…"   
m67bc9xj2xys   redis.2       redis:3.0.7   dock3     Running         Running 17 minutes ago                                          
6qkzjook1yf2    \_ redis.2   redis:3.0.6   dock3     Shutdown        Shutdown 17 minutes ago                                         
qoax6cjko5sh   redis.3       redis:3.0.7   dock1     Running         Running 17 minutes ago                                          
pp0o9rqw16vt    \_ redis.3   redis:3.0.6   dock1     Shutdown        Failed 17 minutes ago        "cannot stop container: redis.…"   
```
The swarm manager maintains the desired state by ending the task on a node with Drain availability and creating a new task on a node with Active availability.

8. Run `docker node update --availability active <NODE-ID>` to return the drained node to an active state:

```bash
james@dock1:~$ docker node update --availability active dock2
dock2
```
9. Inspect the node

```bash
james@dock1:~$ docker node inspect --pretty dock2
ID:			jfe1aktaf71u8yw171mh51en2
Hostname:              	dock2
Joined at:             	2021-08-20 15:32:23.754682853 +0000 utc
Status:
 State:			Ready
 Availability:         	Active
 Address:		192.168.1.170
Platform:
 Operating System:	linux
 Architecture:		x86_64
Resources:
 CPUs:			4
 Memory:		3.835GiB
Plugins:
 Log:		awslogs, fluentd, gcplogs, gelf, journald, json-file, local, logentries, splunk, syslog
 Network:		bridge, host, ipvlan, macvlan, null, overlay
 Volume:		local
Engine Version:		19.03.13
TLS Info:
 TrustRoot:
-----BEGIN CERTIFICATE-----
MIIBajCCARCgAwIBAgIUEYcl+HxbZ02qJz3zzOc4zvgV32QwCgYIKoZIzj0EAwIw
EzERMA8GA1UEAxMIc3dhcm0tY2EwHhcNMjEwODIwMTUyMjAwWhcNNDEwODE1MTUy
MjAwWjATMREwDwYDVQQDEwhzd2FybS1jYTBZMBMGByqGSM49AgEGCCqGSM49AwEH
A0IABMkA2uggLcg+XfQLKgmvErSTfH2QWrWtHxmRwQ5WrchkfgHVPagqcuvd45o4
YMT+AIHrDKBiX9vecN8dBgVE4sGjQjBAMA4GA1UdDwEB/wQEAwIBBjAPBgNVHRMB
Af8EBTADAQH/MB0GA1UdDgQWBBSSKq5xC4Swy7iUduE6Gr3riirGjTAKBggqhkjO
PQQDAgNIADBFAiEAxqD78onxWxfMmXvLxUaGgiwGb5TJq648ZTdR4ekng4ICIDAp
4QzOI8FbUF9WbJ6bHjFFBlnxbXlWI6wqdJ4AAWgN
-----END CERTIFICATE-----

 Issuer Subject:	MBMxETAPBgNVBAMTCHN3YXJtLWNh
 Issuer Public Key:	MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEyQDa6CAtyD5d9AsqCa8StJN8fZBata0fGZHBDlatyGR+AdU9qCpy693jmjhgxP4AgesMoGJf295w3x0GBUTiwQ==
 ```
 
 When you set the node back to `Active` availability, it can receive new tasks:

-   during a service update to scale up
-   during a rolling update
-   when you set another node to `Drain` availability
-   when a task fails on another active node