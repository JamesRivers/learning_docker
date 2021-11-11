- cn_1879044137_containers_vs_vmmachine
	- Created on the: 2021-11-11 09:24
	- Review date:  2022-11-11
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
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / requirements ⚓ before you start you need to know.
---

# Container Vs Virtual Machine 
Google it ... you will find many write ups and videos on what a Virtual Machine is compared to a container. 

Stop thinking about it in that terms.  Instead start thinking about it as `restricted process inside your host operating system`. 

Lets take a look...  🤸 head to your terminal and lets start a mongo db. 
```bash
docker run --name mongo -d mongo  
```
Run docker top and review the process. 
```bash
❯ docker top mongo
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
999                 17408               17382               1                   09:34               ?                   00:00:01            mongod --bind_ip_all
```
If you are running on a linux host running the command `ps aux` show me all running process then you will see the docker mongo process running. You can't run that command and see the same result if you are running docker on mac or windows. 

Everything we have done so far is using containers in Linux base systems.  But you can start to expore docker and Windows containers (why would you?)... But here you go [# Windows Containers and Docker: 101](https://www.youtube.com/watch?v=066-9yw8-7c)




