- cn_1879049061_docker_networks_dns
	- Created on the: 2021-11-11 13:59
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning โ ๏ธ - Important need to know / Notes ๐ - Additional notes / Version ๐ฑ - Specific to a Version / Knowledge ๐ง  - Did you know / WWW ๐ธ - Links to web pages / Learning AIM ๐ฏ - The Learning target for this topic / Evaluation ๐งช - Set of evaluation items to show the target was reached / Exercise ๐คธ - Hands on time... /  Reading ๐  - Items for you to read / Requirements โ before you start you need to know.
---
# docker dns 
Requirements โ - know how to start a container, plus under the TCP/IP stack, subnets, firewalls etc... 

Learning AIM ๐ฏ 
- Understand how DNS is the key to easy inter-container comms
- See how it works by default with custom networks
- Learn how to use --link to enable DNS on default bridge network

**DNS good - IP Bad....** Shocking statement, but in the world of microservices and HA, IP allocations change under orchestration, host names do not...

Knowledge ๐ง  docker daemon has a built in DNS Server that the containers use by default 

Docker uses the container name as the equivilant of the hostname for containers to talk to eachother. 

Exercise ๐คธ `docker container run --name nginxagain --network newnetwork nginx`.  Check the network, we can see 2 container on this network `bridge`.

Exercise ๐คธ open an interactive terminal for the new container and run a ping.  
```bash
learning_docker on ๎  main [!?] on โ๏ธ  (us-east-1) took 6m19s 
โ docker container exec -it nginxagain ping nginxnetwork
PING nginxnetwork (172.19.0.2) 56(84) bytes of data.
64 bytes from nginxnetwork.newnetwork (172.19.0.2): icmp_seq=1 ttl=64 time=0.071 ms
64 bytes from nginxnetwork.newnetwork (172.19.0.2): icmp_seq=2 ttl=64 time=0.354 ms
64 bytes from nginxnetwork.newnetwork (172.19.0.2): icmp_seq=3 ttl=64 time=0.231 ms
64 bytes from nginxnetwork.newnetwork (172.19.0.2): icmp_seq=4 ttl=64 time=0.266 ms
64 bytes from nginxnetwork.newnetwork (172.19.0.2): icmp_seq=5 ttl=64 time=0.290 ms
64 bytes from nginxnetwork.newnetwork (172.19.0.2): icmp_seq=6 ttl=64 time=0.325 ms
```

Warning โ ๏ธ  latest images of nginx have ping removed, fix via an apt-get or use the alpline version of nginx.  

Fix with at a docker exec level:
```bash
apt-get update
apt-get install iputils-ping
```

Note the default bridge network does not have the docker dns in built by default.  So always best to create a new network. Or we can use the `--link` option, but this is deprecated you can read up on this [here](https://docs.docker.com/engine/reference/commandline/network_connect/)




