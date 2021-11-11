- cn_1894949653_docker_network_managment
	- Created on the: 2021-11-11 13:29
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- https://docs.docker.com/engine/tutorials/networkingcontainers/
		- https://docs.docker.com/network/host/
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# docker network management
Requirements ⚓ - know how to start a container, plus under the TCP/IP stack, subnets, firewalls etc... 

Learning AIM 🎯 
- Show networks `docker network ls`
- Inspect a network `docker network inspect`
- Create a network `docker network create --driver`
- Attach a network to container `docker network connect`
- Detach a network from container `docker network disconnect`

## docker network ls
```bash
❯ docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
a6f482f1b123   bridge            bridge    local
6a7f7b35385d   docker_gwbridge   bridge    local
a4e1a5061ae5   host              host      local
b2682996b499   none              null      local
```
the network named `bridge` is the default docker network that containers get assigned to. 

## docker network inspect
run the command `docker network inspect`
```bash
➜ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "a6f482f1b12362f2e782e85b561f1d172506a0204319a11d3b8ce3a40a57ac2c",
        "Created": "2021-11-08T09:00:12.018798924Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "deec717196412fcea38f7d53c92d050d364e05829ab56ef872cb8218bc5f76f9": {
                "Name": "nginx-ports",
                "EndpointID": "5d144bab6df44794b7cf9219f8e1ed15b778a239dbc7e032846cd3e6f293aeef",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

```
Under the `ipam` section we can see the details linked to this network called `bridge`
```bash
 "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        }
```
Knowledge 🧠 the 172.17.0.0/16 network is the default network range for the `bridge` network - this can be changed. 

Other networks beside `bridge` we have another called `host`.  This is a special network, If you use the host network mode for a container, that container’s network stack is not isolated from the Docker host (the container shares the host’s networking namespace), and the container does not get its own IP-address allocated. For instance, if you run a container which binds to port 80 and you use host networking, the container’s application is available on port 80 on the host’s IP address.

Host mode networking can be useful to optimize performance, and in situations where a container needs to handle a large range of ports, as it does not require network address translation (NAT), and no “userland-proxy” is created for each port.

The last network is `none`, this removes the ethernet network on the container. If you want to completely disable the networking stack on a container, you can use the --network none flag when starting the container. Within the container, only the loopback device is created. 

Exercise 🤸 - create a new network called whatever you like, use the command `docker network create newnetwork`
```bash
➜ docker network create newnetwork
af58f54c18a85769f288f3dccfc79b47914b5a9001f7329cfb4011467da58dad

➜ docker network ls               
NETWORK ID     NAME              DRIVER    SCOPE
a6f482f1b123   bridge            bridge    local
6a7f7b35385d   docker_gwbridge   bridge    local
a4e1a5061ae5   host              host      local
af58f54c18a8   newnetwork        bridge    local
b2682996b499   none              null      local
```

Notice the type of the network is a `bridge` that is the default type for new networks. Unless you specifiy a new driver type, it will always be bridge. 

Run the `docker network inspect` cmd on your new network and locate the new IP range / subnet.  I bet it will be 172.18/19.0.0... It gets incremented. 

Make a new container and add that container to the new network you just created.
```bash
❯ docker container run -d --name nginxnetwork --network newnetwork nginx 
6856eb6a5ff97490431d4630df52e3cb7c23b7544c6d8a60745ff9b375d48658
➜ docker network inspect newnetwork
[
    {
        "Name": "newnetwork",
        "Id": "af58f54c18a85769f288f3dccfc79b47914b5a9001f7329cfb4011467da58dad",
        "Created": "2021-11-11T13:42:44.386304963Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "6856eb6a5ff97490431d4630df52e3cb7c23b7544c6d8a60745ff9b375d48658": {
                "Name": "nginxnetwork",
                "EndpointID": "40d536ae3e3ddadd0ae5e67f1bb63e9c9b77abe594a0654c54041460cd6df07f",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```
See that the container is listed here... 

Next we are going to edit the network with `docker network` and connect an existing container to the new network you created. 
```bash
➜ docker network --help

Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
```
So I am going to add a container deec71719641 to my new network, af58f54c18a8. 
```bash
docker network connect af58f54c18a8 deec71719641
```
 Connect the network to the container.  Remember that order. 
 
 Run a `docker container inspect` and review the containers connected to the 2 networks. 
 
 Reverse the process by running a `docker network disconnect` 
 
 ## docker security
- Create your apps so frontend/backend sit on same Docker network
- Their inter-communication never leaves host
- All externally exposed ports closed by default
- You must manually expose via -p, which is better default security!
- This gets even better later with Swarm and Overlay networks
 



Before we go on lets look at the `docker network create -- help`
```bash
➜ docker network create --help

Usage:  docker network create [OPTIONS] NETWORK

Create a network

Options:
      --attachable           Enable manual container attachment
      --aux-address map      Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
      --config-from string   The network from which to copy the configuration
      --config-only          Create a configuration only network
  -d, --driver string        Driver to manage the Network (default "bridge")
      --gateway strings      IPv4 or IPv6 Gateway for the master subnet
      --ingress              Create swarm routing-mesh network
      --internal             Restrict external access to the network
      --ip-range strings     Allocate container ip from a sub-range
      --ipam-driver string   IP Address Management Driver (default "default")
      --ipam-opt map         Set IPAM driver specific options (default map[])
      --ipv6                 Enable IPv6 networking
      --label list           Set metadata on a network
  -o, --opt map              Set driver specific options (default map[])
      --scope string         Control the network's scope
      --subnet strings       Subnet in CIDR format that represents a network segment
```

