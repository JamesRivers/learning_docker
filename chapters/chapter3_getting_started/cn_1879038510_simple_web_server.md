- cn_1879038510_simple_web_server
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-11-10 17:50
	- Review date: 2022-11-10
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- 
	- Sources: 
		- Brett Fisher
	- Author Notes: 
		- 
	- Tags: 
		- 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read. 
---
# Image Vs Container
• An Image is the application we want to run
• A Container is an instance of that image running as a process
• You can have many containers running off the same image
• In this lecture our image will be the Nginx web server
• Docker's default image "registry" is called Docker Hub (hub.docker.com)

## Run 1st Container
To deploy our first container we are going to:
1. Downloaded image 'nginx' from Docker Hub  
2. Started a new container from that image  
3. Opened port 80 on the host IP  
4. Routes that traffic to the container IP, port 80


🤸 To do all this we are going to use a simple command as follows: 
```bash
docker container run --publish 80:80 nginx
```

Open a browser on the same machine and enter localhost. Note we can see the container output.
```bash 
➜ docker container run --publish 80:80 nginx
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

🤸 Repeat this step, but run the container in the background.  `Ctrl+C` in your terminal and then enter the same command but this time include the `--detach` or `-d` option. 

```bash
docker container run --detach --publish 80:80 nginx
```
Is it running? Check the browser.  But how else could you check the state of the container?  Use the docker command `docker container ls` 
```bash 
➜ docker container ls
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                NAMES
899e2103e0b6   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp   jovial_satoshi
```
🤸 Now stop this container?  Use the command `docker container stop <container ID>` this command will stop the container - but it will not remove it.  Run 2 commands and check the output.  `docker container ls` and `docker container ls -a`. The first command only shows you running containers, the 2nd command shows you all containers, even those that are stopped. 

But we get 2? 
```bash
➜ docker container ls -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
899e2103e0b6   nginx     "/docker-entrypoint.…"   5 minutes ago   Exited (0) 2 minutes ago             jovial_satoshi
f12a5997c104   nginx     "/docker-entrypoint.…"   8 minutes ago   Exited (0) 5 minutes ago             serene_lumiere
```
Correct you have run 2 containers, we ran the command twice, first we ran then exited then ran another in detach mode. So we have 2 contaners stopped.  

 🗒  - Note we did not specify a container name, yet we have names here, serene_lumiere & jovial_satoshi, this is an aut generated name for containers if no name is specified. Fun fact this name creation is from a list of adjectives and notable scientists/hackers. 
 
🤸 Run it again, but this time add a name, use the `--name` option. 
```bash
docker container run --detach --publish 80:80 --name simple_server nginx 
```
🤸  List all the containers and review:
```bash
docker container ls -a                                                    
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS                      PORTS                NAMES
ea40e6c8eb42   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute           0.0.0.0:80->80/tcp   simple_server
899e2103e0b6   nginx     "/docker-entrypoint.…"   12 minutes ago       Exited (0) 9 minutes ago                         jovial_satoshi
f12a5997c104   nginx     "/docker-entrypoint.…"   15 minutes ago       Exited (0) 12 minutes ago                        serene_lumiere
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

