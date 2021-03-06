- cn_1879038510_simple_web_server
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-11-10 17:50
	- Review date: 2022-11-10
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- Brett Fisher
	- Author Notes: 
		- 
	- Tags: 
		- #docker
	- ICON set : 
		- Warning โ ๏ธ - Important need to know / Notes ๐ - Additional notes / Version ๐ฑ - Specific to a Version / Knowledge ๐ง  - Did you know / WWW ๐ธ - Links to web pages / Learning AIM ๐ฏ - The Learning target for this topic / Evaluation ๐งช - Set of evaluation items to show the target was reached / Exercise ๐คธ - Hands on time... /  Reading ๐  - Items for you to read / requirements โ before you start you need to know.
---
# Image Vs Container
- An Image is the application we want to run
- A Container is an instance of that image running as a process
- You can have many containers running off the same image
-  In this lecture our image will be the Nginx web server
-  Docker's default image "registry" is called Docker Hub (hub.docker.com)

## Run 1st Container
To deploy our first container we are going to:
1. Downloaded image 'nginx' from Docker Hub  
2. Started a new container from that image  
3. Opened port 80 on the host IP  
4. Routes that traffic to the container IP, port 80


๐คธ To do all this we are going to use a simple command as follows: 
```bash
docker container run --publish 80:80 nginx
```

Open a browser on the same machine and enter localhost. Note we can see the container output.
```bash 
โ docker container run --publish 80:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
a9eb63951c1c: Pull complete 
bcbfef5b4e41: Pull complete 
190e9b3bad3b: Pull complete 
24b4774576cb: Pull complete 
6985cd835388: Pull complete 
41f34e6089e4: Pull complete 
Digest: sha256:dfef797ddddfc01645503cef9036369f03ae920cac82d344d58b637ee861fda1
Status: Downloaded newer image for nginx:latest
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2021/11/10 17:56:53 [notice] 1#1: using the "epoll" event method
2021/11/10 17:56:53 [notice] 1#1: nginx/1.21.4
2021/11/10 17:56:53 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2021/11/10 17:56:53 [notice] 1#1: OS: Linux 5.10.25-linuxkit
2021/11/10 17:56:53 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2021/11/10 17:56:53 [notice] 1#1: start worker processes
2021/11/10 17:56:53 [notice] 1#1: start worker process 32
2021/11/10 17:56:53 [notice] 1#1: start worker process 33
2021/11/10 17:56:53 [notice] 1#1: start worker process 34
2021/11/10 17:56:53 [notice] 1#1: start worker process 35
```

๐คธ Repeat this step, but run the container in the background.  `Ctrl+C` in your terminal and then enter the same command but this time include the `--detach` or `-d` option. 

```bash
docker container run --detach --publish 80:80 nginx
```
Is it running? Check the browser.  But how else could you check the state of the container?  Use the docker command `docker container ls` 
```bash 
โ docker container ls
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                NAMES
899e2103e0b6   nginx     "/docker-entrypoint.โฆ"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp   jovial_satoshi
```
๐คธ Now stop this container?  Use the command `docker container stop <container ID>` this command will stop the container - but it will not remove it.  Run 2 commands and check the output.  `docker container ls` and `docker container ls -a`. The first command only shows you running containers, the 2nd command shows you all containers, even those that are stopped. 

But we get 2? 
```bash
โ docker container ls -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
899e2103e0b6   nginx     "/docker-entrypoint.โฆ"   5 minutes ago   Exited (0) 2 minutes ago             jovial_satoshi
f12a5997c104   nginx     "/docker-entrypoint.โฆ"   8 minutes ago   Exited (0) 5 minutes ago             serene_lumiere
```
Correct you have run 2 containers, we ran the command twice, first we ran then exited then ran another in detach mode. So we have 2 contaners stopped.  

 ๐  - Note we did not specify a container name, yet we have names here, serene_lumiere & jovial_satoshi, this is an aut generated name for containers if no name is specified. Fun fact this name creation is from a list of adjectives and notable scientists/hackers. 
 
๐คธ Run it again, but this time add a name, use the `--name` option. 
```bash
docker container run --detach --publish 80:80 --name simple_server nginx 
```
๐คธ  List all the containers and review:
```bash
docker container ls -a                                                    
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS                      PORTS                NAMES
ea40e6c8eb42   nginx     "/docker-entrypoint.โฆ"   About a minute ago   Up About a minute           0.0.0.0:80->80/tcp   simple_server
899e2103e0b6   nginx     "/docker-entrypoint.โฆ"   12 minutes ago       Exited (0) 9 minutes ago                         jovial_satoshi
f12a5997c104   nginx     "/docker-entrypoint.โฆ"   15 minutes ago       Exited (0) 12 minutes ago                        serene_lumiere
```

Ok good stuff, but now we have the container running in the background how are we going to see the logs?  Good point.  New command , use `docker container logs <id container>`.
```bash 
docker container logs ea 
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2021/11/10 18:10:53 [notice] 1#1: using the "epoll" event method
2021/11/10 18:10:53 [notice] 1#1: nginx/1.21.4
2021/11/10 18:10:53 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2021/11/10 18:10:53 [notice] 1#1: OS: Linux 5.10.25-linuxkit
2021/11/10 18:10:53 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2021/11/10 18:10:53 [notice] 1#1: start worker processes
2021/11/10 18:10:53 [notice] 1#1: start worker process 32
2021/11/10 18:10:53 [notice] 1#1: start worker process 33
2021/11/10 18:10:53 [notice] 1#1: start worker process 34
2021/11/10 18:10:53 [notice] 1#1: start worker process 35
172.17.0.1 - - [10/Nov/2021:18:13:20 +0000] "GET / HTTP/1.1" 200 615 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0" "-"
2021/11/10 18:13:20 [error] 32#32: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost", referrer: "http://localhost/"
172.17.0.1 - - [10/Nov/2021:18:13:20 +0000] "GET /favicon.ico HTTP/1.1" 404 153 "http://localhost/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0" "-"
172.17.0.1 - - [10/Nov/2021:18:13:23 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0" "-"
172.17.0.1 - - [10/Nov/2021:18:13:23 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0" "-"
172.17.0.1 - - [10/Nov/2021:18:13:23 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0" "-"
172.17.0.1 - - [10/Nov/2021:18:13:23 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0" "-"
```

Experiment with the command `docker container logs` to get a differing ouptut. 
```bash
docker container logs --help

Usage:  docker container logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
```

I like follow!

Other methods to check on the container state and health. `docker container top <id container`
```bash
โ docker container top --help 

Usage:  docker container top CONTAINER [ps OPTIONS]

Display the running processes of a container

learning_docker on ๎  main [!] on โ๏ธ  (us-east-1) 
โ docker container top ea    
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                15278               15252               0                   18:10               ?                   00:00:00            nginx: master process nginx -g daemon off;
uuidd               15339               15278               0                   18:10               ?                   00:00:00            nginx: worker process
uuidd               15340               15278               0                   18:10               ?                   00:00:00            nginx: worker process
uuidd               15341               15278               0                   18:10               ?                   00:00:00            nginx: worker process
uuidd               15342               15278               0                   18:10               ?                   00:00:00            nginx: worker process
```

Clean up this mess... and do it quickly. Ok, so we can remove our containers and do some housekeeping.  Use the `docker container rm` command and list multiple containers. 
```bash
โ docker container ls -a      
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS                NAMES
ea40e6c8eb42   nginx     "/docker-entrypoint.โฆ"   12 minutes ago   Up 12 minutes               0.0.0.0:80->80/tcp   simple_server
899e2103e0b6   nginx     "/docker-entrypoint.โฆ"   23 minutes ago   Exited (0) 21 minutes ago                        jovial_satoshi
f12a5997c104   nginx     "/docker-entrypoint.โฆ"   26 minutes ago   Exited (0) 24 minutes ago                        serene_lumiere
โ docker container rm ea 89 f12
89
f12
Error response from daemon: You cannot remove a running container ea40e6c8eb422c47ac2583ca9efdda761e4bf9df09b86dee1f39158b75eaa5c7. Stop the container before attempting removal or force remove
โฏ docker container ls -a       
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                NAMES
ea40e6c8eb42   nginx     "/docker-entrypoint.โฆ"   13 minutes ago   Up 13 minutes   0.0.0.0:80->80/tcp   simple_server
```

You cannot remove a running container - you have been warned.  But do you have the force?  `docker container rm -f <id container>`
```bash
docker container rm ea -f 
```

## What happens in docker container run
- looks for an image locally in the image cache
- nothing located, it will look in the remote image repository - default is docker hub
	- note we can add other repos as required. 
- downloads the latest version of the imge as we did specifiy a version tag. 
- it created a new container based on that image and prepares to start. 
- a new virtual IP is allocated to the conrtainer on the default bridge docker network. 
- opens a port 80, on the host and forwards to port 80 in the container
- starts container by using the CMD in the image dockerfile





