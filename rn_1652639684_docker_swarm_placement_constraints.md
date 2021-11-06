- rn_1652639684_docker_swarm_placement_constraints
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-09-08 13:28
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
#### Placement constraints

Use placement constraints to control the nodes a service can be assigned to. In the following example, the service only runs on nodes with the [label](https://docs.docker.com/engine/swarm/manage-nodes/#add-or-remove-label-metadata) `region` set to `east`. If no appropriately-labelled nodes are available, tasks will wait in `Pending` until they become available. The `--constraint` flag uses an equality operator (`==` or `!=`). For replicated services, it is possible that all services run on the same node, or each node only runs one replica, or that some nodes don’t run any replicas. For global services, the service runs on every node that meets the placement constraint and any [resource requirements](https://docs.docker.com/engine/swarm/services/#reserve-memory-or-cpus-for-a-service).

```
 docker service create \
  --name my-nginx \
  --replicas 5 \
  --constraint node.labels.region==east \
  nginx
```

You can also use the `constraint` service-level key in a `docker-compose.yml` file.

If you specify multiple placement constraints, the service only deploys onto nodes where they are all met. The following example limits the service to run on all nodes where `region` is set to `east` and `type` is not set to `devel`:

```
 docker service create \
  --name my-nginx \
  --mode global \
  --constraint node.labels.region==east \
  --constraint node.labels.type!=devel \
  nginx
```

You can also use placement constraints in conjunction with placement preferences and CPU/memory constraints. Be careful not to use settings that are not possible to fulfill.

For more information on constraints, refer to the `docker service create` [CLI reference](https://docs.docker.com/engine/reference/commandline/service_create/).


### Docker Labels 
Add 
```bash
 docker node update --label-add region=region-a swarm-test-01
 docker node update --label-add region=region-a swarm-test-02
 docker node update --label-add region=region-b swarm-test-03
 docker node update --label-add region=region-b swarm-test-04
 ```

```bash
docker node update dockernode --label-add wyc-dev=true
```

Check 
```bash
james@wyc-101-dock:/etc/imagine/content$ docker node ls --filter node.label=wyc-dev
ID                            HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
vv82v60pygdmlt2svc615vatm *   wyc-101-dock   Ready     Active         Leader           20.10.8
t2y94shs1xy64mi45popkbd4m     wyc-102-dock   Ready     Active                          20.10.8
old97teln9ja9xtmrvarsykra     wyc-103-dock   Ready     Active                          20.10.8
```



examples 

#### Placement preferences

While [placement constraints](https://docs.docker.com/engine/swarm/services/#placement-constraints) limit the nodes a service can run on, _placement preferences_ try to place tasks on appropriate nodes in an algorithmic way (currently, only spread evenly). For instance, if you assign each node a `rack` label, you can set a placement preference to spread the service evenly across nodes with the `rack` label, by value. This way, if you lose a rack, the service is still running on nodes on other racks.

Placement preferences are not strictly enforced. If no node has the label you specify in your preference, the service is deployed as though the preference were not set.

> Placement preferences are ignored for global services.

The following example sets a preference to spread the deployment across nodes based on the value of the `datacenter` label. If some nodes have `datacenter=us-east` and others have `datacenter=us-west`, the service is deployed as evenly as possible across the two sets of nodes.

```
 docker service create \
  --replicas 9 \
  --name redis_2 \
  --placement-pref 'spread=node.labels.datacenter' \
  redis:3.0.6
```

> Missing or null labels
> 
> Nodes which are missing the label used to spread still receive task assignments. As a group, these nodes receive tasks in equal proportion to any of the other groups identified by a specific label value. In a sense, a missing label is the same as having the label with a null value attached to it. If the service should **only** run on nodes with the label being used for the spread preference, the preference should be combined with a constraint.

You can specify multiple placement preferences, and they are processed in the order they are encountered. The following example sets up a service with multiple placement preferences. Tasks are spread first over the various datacenters, and then over racks (as indicated by the respective labels):

```
 docker service create \
  --replicas 9 \
  --name redis_2 \
  --placement-pref 'spread=node.labels.datacenter' \
  --placement-pref 'spread=node.labels.rack' \
  redis:3.0.6
```

You can also use placement preferences in conjunction with placement constraints or CPU/memory constraints. Be careful not to use settings that are not possible to fulfill.

This diagram illustrates how placement preferences work:

![](/attachments/Pasted%20image%2020210908132922.png)

When updating a service with `docker service update`, `--placement-pref-add` appends a new placement preference after all existing placement preferences. `--placement-pref-rm` removes an existing placement preference that matches the argument.