- rn_1595389489_docker_swarm_about
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-20 15:52
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://docs.docker.com/engine/swarm/
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Docker Swarm
- What?
	- Docker swarm is **a container orchestration tool**, meaning that it allows the user to manage multiple containers deployed across multiple host machines. One of the key benefits associated with the operation of a docker swarm is the high level of availability offered for applications.
- How?
	- Current versions of Docker include _swarm mode_ for natively managing a cluster of Docker Engines called a _swarm_. Use the Docker CLI to create a swarm, deploy application services to a swarm, and manage swarm behavior.
- Why? 
	- We are in production - I want HA, I want to make sure that the container is up and contactable. Docker Swarm is the easier orchestration to get to grips with - yes we will go through `k8s`
## Swarm Features 
-   **Cluster management integrated with Docker Engine:** Use the Docker Engine CLI to create a swarm of Docker Engines where you can deploy application services. You don’t need additional orchestration software to create or manage a swarm.
    
-   **Decentralized design:** Instead of handling differentiation between node roles at deployment time, the Docker Engine handles any specialization at runtime. You can deploy both kinds of nodes, managers and workers, using the Docker Engine. This means you can build an entire swarm from a single disk image.
    
-   **Declarative service model:** Docker Engine uses a declarative approach to let you define the desired state of the various services in your application stack. For example, you might describe an application comprised of a web front end service with message queueing services and a database backend.
    
-   **Scaling:** For each service, you can declare the number of tasks you want to run. When you scale up or down, the swarm manager automatically adapts by adding or removing tasks to maintain the desired state.
    
-   **Desired state reconciliation:** The swarm manager node constantly monitors the cluster state and reconciles any differences between the actual state and your expressed desired state. For example, if you set up a service to run 10 replicas of a container, and a worker machine hosting two of those replicas crashes, the manager creates two new replicas to replace the replicas that crashed. The swarm manager assigns the new replicas to workers that are running and available.
    
-   **Multi-host networking:** You can specify an overlay network for your services. The swarm manager automatically assigns addresses to the containers on the overlay network when it initializes or updates the application.
    
-   **Service discovery:** Swarm manager nodes assign each service in the swarm a unique DNS name and load balances running containers. You can query every container running in the swarm through a DNS server embedded in the swarm.
    
-   **Load balancing:** You can expose the ports for services to an external load balancer. Internally, the swarm lets you specify how to distribute service containers between nodes.
    
-   **Secure by default:** Each node in the swarm enforces TLS mutual authentication and encryption to secure communications between itself and all other nodes. You have the option to use self-signed root certificates or certificates from a custom root CA.
    
-   **Rolling updates:** At rollout time you can apply service updates to nodes incrementally. The swarm manager lets you control the delay between service deployment to different sets of nodes. If anything goes wrong, you can roll back to a previous version of the service.

## [Key Concepts](https://docs.docker.com/engine/swarm/key-concepts/#what-is-a-swarm)

The cluster management and orchestration features embedded in the Docker Engine are built using [swarmkit](https://github.com/docker/swarmkit/). 

`Swarmkit`is a separate project which implements Docker’s orchestration layer and is used directly within Docker.

A swarm consists of multiple Docker hosts which run in **swarm mode** and act as managers (to manage membership and delegation) and workers (which run [swarm services](https://docs.docker.com/engine/swarm/key-concepts/#services-and-tasks)). 

A given Docker host can be a manager, a worker, or perform both roles. When you create a service, you define its optimal state (number of replicas, network and storage resources available to it, ports the service exposes to the outside world, and more). Docker works to maintain that desired state. For instance, if a worker node becomes unavailable, Docker schedules that node’s tasks on other nodes. A _task_ is a running container which is part of a swarm service and managed by a swarm manager, as opposed to a standalone container.

One of the key advantages of swarm services over standalone containers is that you can modify a service’s configuration, including the networks and volumes it is connected to, without the need to manually restart the service. Docker will update the configuration, stop the service tasks with the out of date configuration, and create new ones matching the desired configuration.

When Docker is running in swarm mode, you can still run standalone containers on any of the Docker hosts participating in the swarm, as well as swarm services. A key difference between standalone containers and swarm services is that only swarm managers can manage a swarm, while standalone containers can be started on any daemon. Docker daemons can participate in a swarm as managers, workers, or both.

In the same way that you can use [Docker Compose](https://docs.docker.com/compose/) to define and run containers, you can define and run [Swarm service](https://docs.docker.com/engine/swarm/services/) stacks.

Keep reading for details about concepts relating to Docker swarm services, including nodes, services, tasks, and load balancing.

## Nodes[](https://docs.docker.com/engine/swarm/key-concepts/#nodes)

A **node** is an instance of the Docker engine participating in the swarm. You can also think of this as a Docker node. You can run one or more nodes on a single physical computer or cloud server, but production swarm deployments typically include Docker nodes distributed across multiple physical and cloud machines.

To deploy your application to a swarm, you submit a service definition to a **manager node**. The manager node dispatches units of work called [tasks](https://docs.docker.com/engine/swarm/key-concepts/#services-and-tasks) to worker nodes.

Manager nodes also perform the orchestration and cluster management functions required to maintain the desired state of the swarm. Manager nodes elect a single leader to conduct orchestration tasks.

**Worker nodes** receive and execute tasks dispatched from manager nodes. By default manager nodes also run services as worker nodes, but you can configure them to run manager tasks exclusively and be manager-only nodes. An agent runs on each worker node and reports on the tasks assigned to it. The worker node notifies the manager node of the current state of its assigned tasks so that the manager can maintain the desired state of each worker.

## Services and tasks[](https://docs.docker.com/engine/swarm/key-concepts/#services-and-tasks)

A **service** is the definition of the tasks to execute on the manager or worker nodes. It is the central structure of the swarm system and the primary root of user interaction with the swarm.

When you create a service, you specify which container image to use and which commands to execute inside running containers.

In the **replicated services** model, the swarm manager distributes a specific number of replica tasks among the nodes based upon the scale you set in the desired state.

For **global services**, the swarm runs one task for the service on every available node in the cluster.

A **task** carries a Docker container and the commands to run inside the container. It is the atomic scheduling unit of swarm. Manager nodes assign tasks to worker nodes according to the number of replicas set in the service scale. Once a task is assigned to a node, it cannot move to another node. It can only run on the assigned node or fail.

## Load balancing[](https://docs.docker.com/engine/swarm/key-concepts/#load-balancing)

The swarm manager uses **ingress load balancing** to expose the services you want to make available externally to the swarm. The swarm manager can automatically assign the service a **PublishedPort** or you can configure a PublishedPort for the service. You can specify any unused port. If you do not specify a port, the swarm manager assigns the service a port in the 30000-32767 range.

External components, such as cloud load balancers, can access the service on the PublishedPort of any node in the cluster whether or not the node is currently running the task for the service. All nodes in the swarm route ingress connections to a running task instance.

Swarm mode has an internal DNS component that automatically assigns each service in the swarm a DNS entry. The swarm manager uses **internal load balancing** to distribute requests among services within the cluster based upon the DNS name of the service.



