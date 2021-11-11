- cn_1879048084_shell_into_containers_no_ssh
	- Created on the: 2021-11-11 10:24
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
	- Sources: 
		- Brett Fisher
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read. 
---
# Access your containers - ssh?
You know how to start a container? No start at the beginning of this chapter. 

Commands we are going to use here:
- `docker container run -it` - start a new container interactively
- `docker container exec -it` - run an additional command in an existing container

🤸  - have your 2 containers running from the previous module. 

🧠  What is `-it` t= psuedo-tty, like a real terminal, i= interactive, keeps the terminal open. 

🤸  run a new nginx container with additional commands:
`Usage: docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]`
so run a new container and connect with a terminal and use the additional command to launch bash.
```bash
➜ docker container run -it --name nginx-new nginx bash 
root@54dbbe90aae6:/# 
```
I am placed in a new prompt using the user root. I am acting as root on the container.  the number after is the container id.  To get out the shell type `exit` in this state this will stop the container beacuse you are stopping the bash command that you ran. 

Try this again, but with a `ubuntu` image. 
```bash
➜ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
a39c84e173f0: Already exists 
Digest: sha256:626ffe58f6e7566e00254b638eb7e0f3b11d4da9675088f4781a50ae288f3322
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
```

```bash
➜ docker container run -it --name ubuntu ubuntu
root@083c55c73696:/# 
```
bang we are in the ubuntu container.  You can treat this container just as you would a normal linux install, kinda, no systemd in containers.  You can run and install applications via the `apt` package manager. 

Exit this container and again the container will be stopped. 
```bash
➜ docker container ls -a                       
\CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS                     PORTS                                     NAMES
083c55c73696   ubuntu               "bash"                   2 minutes ago    Exited (0) 3 seconds ago                                             ubuntu
```
Run this container to get it back. How? Use the `docker container start -ai` command. 
- `-a`, `--attach`               Attach STDOUT/STDERR and forward signals
- `-i`, `--interactive`      Attach container's STDIN
```bash
learning_docker on  main [!?] on ☁️  (us-east-1) 
❯ docker container start 08
08

learning_docker on  main [!?] on ☁️  (us-east-1) 
➜ docker container ls -a   
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS                     PORTS                                     NAMES
083c55c73696   ubuntu               "bash"                   3 minutes ago    Up 7 seconds                                                         ubuntu
```

But you want to get back to the terminal in an already running container?  This is where `docker exec` comes in.  We want to connect the running mysql container. 
```bash 
learning_docker on  main [!?] on ☁️  (us-east-1) took 1m7s 
➜ docker container ls -a        
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS                      PORTS                                     NAMES
083c55c73696   ubuntu               "bash"                   8 minutes ago    Exited (0) 8 seconds ago                                              ubuntu
54dbbe90aae6   nginx                "/docker-entrypoint.…"   12 minutes ago   Exited (0) 11 minutes ago                                             nginx-new
fec3b5478c07   nginx                "/docker-entrypoint.…"   32 minutes ago   Up 32 minutes               0.0.0.0:80->80/tcp                        nginx-test
86ed817de21d   mysql/mysql-server   "/entrypoint.sh mysq…"   33 minutes ago   Up 33 minutes (healthy)     0.0.0.0:3306->3306/tcp, 33060-33061/tcp   mysql-test
```

run `--help` on `exec` to get help... 

```bash
❯ docker container exec --help

Usage:  docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]

Run a command in a running container

Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a container
  -e, --env list             Set environment variables
      --env-file list        Read in a file of environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir string       Working directory inside the container
```

So the cmd we are going to use is:
```bash
docker container exec --it mysql-test bash
```
Now when you exit this bash terminal, the mysql container should still be running as the bash command was an additonal command on top of the original command to run the container. 

Lastly do this again, but with alpine and not ubuntu image
```bash
docker container run -it --name alpine-test alpine bash
docker: Error response from daemon: OCI runtime create failed: container_linux.go:367: starting container process caused: exec: "bash": executable file not found in $PATH: unknown.
```
THIS IS WILL FAIL - WHY? `bash` is not a valid `shell` in the alpine image. We can only run items in the container that exist in the image.  Apine uses `sh` not a full fat shell, note alpine image is super skinny. 















