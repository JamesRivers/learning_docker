- fn_lvm_resize_ubuntu_server
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-18 11:43
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
	- Author Notes: 
	- Tags: 
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---

Fell down a hole today. LVM on the Container VM host was full.  Why I have a 50g disk

Fix -

```bash
# We need to resize the logical volume to use all the existing and free space of the volume group
$ lvm
lvm> lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
lvm> exit

# And then, we need to resize the file system to use the new available space in the logical volume
$ resize2fs /dev/ubuntu-vg/ubuntu-lv
resize2fs 1.44.1 (24-Mar-2018)
Filesystem at /dev/ubuntu-vg/ubuntu-lv is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 58
The filesystem on /dev/ubuntu-vg/ubuntu-lv is now 120784896 (4k) blocks long.

# Finally, you can check that you now have available space:
$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
udev                               3.9G     0  3.9G   0% /dev
tmpfs                              786M  1.2M  785M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  454G  3.8G  432G   1% /
```

Find top 5 directories taking up space
`du -a /home | sort -n -r | head -n 5`

## Issues experienced
Ubuntu VM suffered a disk space shortage. LVM had 25GB, free =304MB. Physical Disk is 50Gb. 

Ran the following to clear
```bash
lvm 
lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```

```bash
Size of logical volume ubuntu-vg/ubuntu-lv changed from 24.50 GiB (6272 extents) to <49.00 GiB (12543 extents).

Logical volume ubuntu-vg/ubuntu-lv successfully resized.
```

```bash
exit
```

```bash
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

Reboot

All the docker images and containers are not listed. 

```bash
      
docker container ls -a 

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES

      

docker image ls -a 

REPOSITORY TAG IMAGE ID CREATED SIZE

```
Why?  I am not sure. But I re ran the docker compose for the application that I needed to be running. 

But the container could not start

`driver failed programming external connectivity on endpoint`

`Error starting userland proxy: listen tcp 0.0.0.0:9081: bind: address already in use`

That is not true as the container is not running - so what the hell is going on here?  Who has that port? 
```bash
james@testcont1:~/dockercompose/guacamole$ sudo netstat -pna | grep 9081

tcp 0 0 0.0.0.0:9081 0.0.0.0:* LISTEN 3902/docker-proxy 

tcp6 0 0 :::9081 :::* LISTEN 3926/docker-proxy

```

It is held by docker-proxy?  What is that? - Note to self must read more on this. 
- https://windsock.io/the-docker-proxy/


> When a container starts with its port forwarded to the Docker host on which it runs, in addition to the new process that runs inside the container, you may have noticed an additional process on the Docker host called `docker-proxy`:


> The purpose of this process is to enable a service consumer to communicate with the service providing container .... but it's only used in particular circumstances. The `docker-proxy` operates in [userland](http://www.catb.org/jargon/html/U/userland.html), and simply receives any packets arriving at the host's specified port, that the kernel hasn't 'dropped' or forwarded, and redirects them to the container's port. The `docker-proxy` is the same binary as the Docker daemon and Docker client, which the Docker daemon 'reexecs' when it is required.

Next I wanted to find the PID tied to the process.
```bash
james@testcont1:~/dockercompose/guacamole$ sudo lsof -i tcp:9081

COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

docker-pr 3902 root 4u IPv4 41392 0t0 TCP *:9081 (LISTEN)

docker-pr 3926 root 4u IPv6 41403 0t0 TCP *:9081 (LISTEN)
```

So what do I do - restart docker? 
```bash
systemctl stop docker.service
systemctl start docker.service
```

Port 9081 - still held by docker-proxy.

Ok why did I lose all containers and all images when the LVM issue occurred. 

Clean the slate - remove all container and images ... 

```bash
docker kill $(docker ps -q)
```

```bash
docker rm $(docker ps -a -q)
```

```bash
docker rmi $(docker images -q)
```

Final step - kill the PID 
```bash
kill -9 PID
```

```bash
james@testcont1:~/dockercompose/guacamole$ sudo netstat -pna | grep 9081
tcp        0      0 0.0.0.0:9081            0.0.0.0:*               LISTEN      7880/docker-proxy   
tcp6       0      0 :::9081                 :::*                    LISTEN      7887/docker-proxy   
james@testcont1:~/dockercompose/guacamole$ sudo lsof -i tcp:9081
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker-pr 7880 root    4u  IPv4  57234      0t0  TCP *:9081 (LISTEN)
docker-pr 7887 root    4u  IPv6  60742      0t0  TCP *:9081 (LISTEN)
james@testcont1:~/dockercompose/guacamole$ kill -9 7880
-bash: kill: (7880) - Operation not permitted
james@testcont1:~/dockercompose/guacamole$ sudo !!
sudo kill -9 7880
james@testcont1:~/dockercompose/guacamole$ sudo kill -9 7887
james@testcont1:~/dockercompose/guacamole$ sudo lsof -i tcp:9081
james@testcont1:~/dockercompose/guacamole$ 
```

Try again - SUCCESS

---

So there are 2 stories here:
- LVM disk issues and resize - wiped the images and containers on Ubuntu VM Server? 
- Docker-Proxy is still listing the port from previous containers... 