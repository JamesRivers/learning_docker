- fn_docker_alpine
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-05-21 23:40
	- Created by: James Rivers
	- Written against (version): 
	- Sources: 
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
***
1.  To install Docker on Alpine Linux, run apk add docker.
    
    Important
    
    The Docker package is available in the Community repository. Therefore, if apk add fails because of unsatisfiable constraints error, you need to edit the **/etc/apk/repositories** file to add (or uncomment) a line. Community repository link: [http://dl-cdn.alpinelinux.org/alpine/latest-stable/community](http://dl-cdn.alpinelinux.org/alpine/latest-stable/community).
    
2.  To index the repository, run apk update.
3.  To start the Docker daemon at boot, run rc-update add docker boot.
4.  To start the Docker daemon manually, run service docker start.
5.  Execute service docker status to ensure the status is **running**.

rc-update add docker boot
service docker start

## Docker Config 
- https://docs.docker.com/engine/swarm/swarm-tutorial/

## Docker Swarm
- https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/

## Docker Nodes
https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/