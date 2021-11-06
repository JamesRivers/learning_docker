- rn_1595401251_docker_swarm_install
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-20 16:06
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- [Swarm tutorial](https://docs.docker.com/engine/swarm/swarm-tutorial/)
		- [Create Swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/)
		- [Add nodes](https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/)
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
Learning AIM 🎯
- How to deploy and configure docker swarm.

---
# Docker Swarm Setup
The tutorial guides you through the following activities:

-   initializing a cluster of Docker Engines in swarm mode
-   adding nodes to the swarm
-   deploying application services to the swarm
-   managing the swarm once you have everything running.

This tutorial uses Docker Engine CLI commands entered on the command line of a terminal window.

If you are brand new to Docker, see [About Docker Engine](https://docs.docker.com/engine/).

## Set up[](https://docs.docker.com/engine/swarm/swarm-tutorial/#set-up)

To run this tutorial, you need the following:

-   [three Linux hosts which can communicate over a network, with Docker installed](https://docs.docker.com/engine/swarm/swarm-tutorial/#three-networked-host-machines)
-   [the IP address of the manager machine](https://docs.docker.com/engine/swarm/swarm-tutorial/#the-ip-address-of-the-manager-machine)
-   [open ports between the hosts](https://docs.docker.com/engine/swarm/swarm-tutorial/#open-protocols-and-ports-between-the-hosts)

### Three networked host machines[](https://docs.docker.com/engine/swarm/swarm-tutorial/#three-networked-host-machines)

This tutorial requires three Linux hosts which have Docker installed and can communicate over a network. These can be physical machines, virtual machines, Amazon EC2 instances, or hosted in some other way. You can even use Docker Machine from a Linux, Mac, or Windows host. Check out [Getting started - Swarms](https://docs.docker.com/get-started/swarm-deploy/#prerequisites) for one possible set-up for the hosts.

One of these machines is a manager (called `manager1`) and two of them are workers (`worker1` and `worker2`).

> **Note**: You can follow many of the tutorial steps to test single-node swarm as well, in which case you need only one host. Multi-node commands do not work, but you can initialize a swarm, create services, and scale them.

#### Install Docker Engine on Linux machines

If you are using Linux based physical computers or cloud-provided computers as hosts, simply follow the [Linux install instructions](https://docs.docker.com/engine/install/) for your platform. Spin up the three machines, and you are ready. You can test both single-node and multi-node swarm scenarios on Linux machines.

Or you can use my Docker install script - [rn_1595405568_docker_install_script](rn_1595405568_docker_install_script.md)

#### Use Docker Desktop for Mac or Docker Desktop for Windows

Alternatively, install the latest [Docker Desktop for Mac](https://docs.docker.com/desktop/mac/) or [Docker Desktop for Windows](https://docs.docker.com/desktop/windows/) application on one computer. You can test both single-node and multi-node swarm from this computer, but you need to use Docker Machine to test the multi-node scenarios.

-   You can use Docker Desktop for Mac or Windows to test _single-node_ features of swarm mode, including initializing a swarm with a single node, creating services, and scaling services.
-   Currently, you cannot use Docker Desktop for Mac or Docker Desktop for Windows alone to test a _multi-node_ swarm, but many examples are applicable to a single-node Swarm setup.

### The IP address of the manager machine[](https://docs.docker.com/engine/swarm/swarm-tutorial/#the-ip-address-of-the-manager-machine)

The IP address must be assigned to a network interface available to the host operating system. All nodes in the swarm need to connect to the manager at the IP address.

Because other nodes contact the manager node on its IP address, you should use a fixed IP address.

You can run `ifconfig` on Linux or macOS to see a list of the available network interfaces.

If you are using Docker Machine, you can get the manager IP with either `docker-machine ls` or `docker-machine ip <MACHINE-NAME>` — for example, `docker-machine ip manager1`.

The tutorial uses `manager1` : `192.168.1.169`.

- Host1 
	- dock1 
	- 192.168.1.169
	- manager1
- Host2
	- dock2
	- 192.168.1.170
	- worker1
- Host3
	- dock3 
	- 192.168.1.171
	- worker2

### Open protocols and ports between the hosts[](https://docs.docker.com/engine/swarm/swarm-tutorial/#open-protocols-and-ports-between-the-hosts)

The following ports must be available. On some systems, these ports are open by default.

-   **TCP port 2377** for cluster management communications
-   **TCP** and **UDP port 7946** for communication among nodes
-   **UDP port 4789** for overlay network traffic

If you plan on creating an overlay network with encryption (`--opt encrypted`), you also need to ensure **ip protocol 50** (**ESP**) traffic is allowed.

# Create a Swarm
man pages - docker swarm init
```bash
NAME
       docker-swarm-init - Initialize a swarm

SYNOPSIS
       docker swarm init [OPTIONS]

DESCRIPTION
       Initialize a swarm

OPTIONS
       --advertise-addr=""      Advertised address (format: [:port])

       --autolock[=false]      Enable manager autolocking (requiring an unlock key to start a stopped manager)

       --availability="active"      Availability of the node ("active"|"pause"|"drain")

       --cert-expiry=2160h0m0s      Validity period for node certificates (ns|us|ms|s|m|h)

       --data-path-addr=""      Address or interface to use for data path traffic (format: )

       --data-path-port=0      Port number to use for data path traffic (1024 - 49151). If no value is set or is set to 0, the default port (4789) is used.

       --default-addr-pool=[]      default address pool in CIDR format

       --default-addr-pool-mask-length=24      default address pool subnet mask length

       --dispatcher-heartbeat=5s      Dispatcher heartbeat period (ns|us|ms|s|m|h)

       --external-ca=      Specifications of one or more certificate signing endpoints

       --force-new-cluster[=false]      Force create a new cluster from current state

       -h, --help[=false]      help for init

       --listen-addr=0.0.0.0:2377      Listen address (format: [:port])

       --max-snapshots=0      Number of additional Raft snapshots to retain

       --snapshot-interval=10000      Number of log entries between Raft snapshots

       --task-history-limit=5      Task history retention limit
```

1. Open a terminal and ssh into the machine where you want to run your manager node. 
```bash
ssh user@node1
```
2. Run the following command to create a new swarm:
```bash
docker swarm init --advertise-addr <MANAGER-IP>
```

> **Note**: If you are using Docker Desktop for Mac or Docker Desktop for Windows to test single-node swarm, simply run `docker swarm init` with no arguments. There is no need to specify `--advertise-addr` in this case. To learn more, see the topic on how to [Use Docker Desktop for Mac or Docker Desktop for Windows](https://docs.docker.com/engine/swarm/swarm-tutorial/#use-docker-desktop-for-mac-or-docker-desktop-for-windows) with Swarm.

That command creates a swarm on the `manager1` machine:

```bash
james@dock1:~$ docker swarm init --advertise-addr 192.168.1.169
Swarm initialized: current node (shrahbxphxqyu8t4acb2ples4) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3v3a40gqzxptadqgaixyo1d3j7n9le3gf6f37vf0bpa8ckskng-9z2q21oowbr7uuzn9hw1etjve 192.168.1.169:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

The --advertise-addr flag configures the manager node to publish its address as 192.168.99.100. The other nodes in the swarm must be able to access the manager at the IP address.

The output includes the commands to join new nodes to the swarm. Nodes will join as managers or workers depending on the value for the --token flag.

3. Run docker info to view the current state of the swarm:
```bash
james@dock1:~$ docker info
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Build with BuildKit (Docker Inc., v0.6.1-docker)
  scan: Docker Scan (Docker Inc., v0.8.0)

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 19.03.13
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: active
  NodeID: shrahbxphxqyu8t4acb2ples4
  Is Manager: true
  ClusterID: y2a8f7w1svq4nlal2sdarvn32
  Managers: 1
  Nodes: 1
  Default Address Pool: 10.0.0.0/8  
  SubnetSize: 24
  Data Path Port: 4789
  Orchestration:
   Task History Retention Limit: 5
  Raft:
   Snapshot Interval: 10000
   Number of Old Snapshots to Retain: 0
   Heartbeat Tick: 1
   Election Tick: 10
  Dispatcher:
   Heartbeat Period: 5 seconds
  CA Configuration:
   Expiry Duration: 3 months
   Force Rotate: 0
  Autolock Managers: false
  Root Rotation In Progress: false
  Node Address: 192.168.1.169
  Manager Addresses:
   192.168.1.169:2377
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 8fba4e9a7d01810a393d5d25a3621dc101981175
 runc version: 
 init version: fec3683
 Security Options:
  apparmor
  seccomp
   Profile: default
 Kernel Version: 5.11.0-31-generic
 Operating System: Ubuntu Core 18
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 3.835GiB
 Name: dock1
 ID: ZWTS:A3JE:ESMX:XKWD:WIUY:3MYG:3PEI:M67L:P5RN:4BNC:3IXF:HAP6
 Docker Root Dir: /var/snap/docker/common/var-lib-docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
 ```
 4. Run the `docker node ls` command to view information about nodes:
```bash
james@dock1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
shrahbxphxqyu8t4acb2ples4 *   dock1      Ready     Active         Leader           19.03.13
```
The `*` next to the node ID indicates that you’re currently connected on this node.

Docker Engine swarm mode automatically names the node for the machine host name. 

The tutorial covers other columns in later steps.

# Add nodes to the swarm
Once you’ve [created a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/) with a manager node, you’re ready to add worker nodes.

1.  Open a terminal and ssh into the machine where you want to run a worker node. This tutorial uses the name `worker1`.
    
2.  Run the command produced by the `docker swarm init` output from the [Create a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/) tutorial step to create a worker node joined to the existing swarm:
```bash
james@dock2:~$  docker swarm join --token SWMTKN-1-3v3a40gqzxptadqgaixyo1d3j7n9le3gf6f37vf0bpa8ckskng-9z2q21oowbr7uuzn9hw1etjve 192.168.1.169:2377
This node joined a swarm as a worker.
```
TIP - If you don’t have the command available, you can run the following command `docker swarm join-token worker` on a manager node to retrieve the join command for a worker:
```bash
james@dock1:~$ docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3v3a40gqzxptadqgaixyo1d3j7n9le3gf6f37vf0bpa8ckskng-9z2q21oowbr7uuzn9hw1etjve 192.168.1.169:2377
```
3. Open a terminal and ssh into the machine where you want to run a second worker node. This tutorial uses the name `worker2`.
```bash
james@dock3:~$ docker swarm join --token SWMTKN-1-3v3a40gqzxptadqgaixyo1d3j7n9le3gf6f37vf0bpa8ckskng-9z2q21oowbr7uuzn9hw1etjve 192.168.1.169:2377
This node joined a swarm as a worker.
```
4. Open a terminal and ssh into the machine where the manager node runs and run the `docker node ls` command to see the worker nodes:
```bash
james@dock1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
shrahbxphxqyu8t4acb2ples4 *   dock1      Ready     Active         Leader           19.03.13
jfe1aktaf71u8yw171mh51en2     dock2      Ready     Active                          19.03.13
nm7jt9j73ndmz55b1izmmzdy4     dock3      Ready     Active                          20.10.8
```
The `MANAGER` column identifies the manager nodes in the swarm. The empty status in this column for `worker1` and `worker2` identifies them as worker nodes.

Swarm management commands like `docker node ls` only work on manager nodes.
