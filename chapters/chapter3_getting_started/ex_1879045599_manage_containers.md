- ex_1879045599_manage_containers
	- Created on the: 2021-11-11 09:48
	- Review date:
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
# Exercise - manage multiple containers
Learning AIM 🎯 - how to create and start multiple containers.

You are going to build and manage multiple containers.  Here is what you need to deploy:
- nginx 
- mysql 
- httpd (apache) server

Ports, nginx needs to listen on port 80 so that would be 80:80, httpd needs to listen on port 80 also, but we are alreday using 80 on nginx, so map httpd as 8080:80, host on 8080 mapped to container port 80.  MySQL will listen on the default 3306 tcp port.  These cotainers are not talking to each other yet... We just need to get them started to get the pratice.

🧠  remember docs.docker.com and --help are your best friends... Use them!

 🗒 Tips! - use `--detach` `-d` and get into the habit of using `--name` to name your containers. 
 
 An item we haven't reviewed yet is the `env` options. If we pass `--env` or `-e` we can pass environment variables to the container, this is a common practice. For example `-e MYSQL_RANDOM_ROOT_PASSWORD=yes`.  Set this and try and discover the root password by using the `docker logs` command. 
 
 Once you have completed the exercise clean up the mess. 
 
 Try not to peek at the steps as to how I would do it below... 
 
 --- 
 
 ## Steps 
 
 ```bash
 learning_docker on  main [!?] on ☁️  (us-east-1) 
➜ docker run --name nginx-test -d -p 80:80 nginx
4df989e45d99cfc23cac84073800e72c34a9fdf27e3f98f41163e8b9c1475d89

learning_docker on  main [!?] on ☁️  (us-east-1) 
➜ docker run --name httpd-test -d -p 8080:80 httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
a9eb63951c1c: Already exists 
6f84cbe7322a: Pull complete 
0420072fc7d8: Pull complete 
f4210a0a1463: Pull complete 
02a1ec8e1aad: Pull complete 
Digest: sha256:f70876d78442771406d7245b8d3425e8b0a86891c79811af94fb2e12af0fadeb
Status: Downloaded newer image for httpd:latest
80df0e5e394eec772d4e3c3388b6dce7e1d6d8ba31f734e9ae156906efedfe58

learning_docker on  main [!?] on ☁️  (us-east-1) took 8s 
➜ docker run --name mysql-test -d -p 3306:3306 -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql 
Unable to find image 'mysql:latest' locally
latest: Pulling from library/mysql
docker: no matching manifest for linux/arm64/v8 in the manifest list entries.
See 'docker run --help'.
      
learning_docker on  main [!?] on ☁️  (us-east-1) took 3s 
❯ docker run --name mysql-test -d -p 3306:3306 -e MYSQL_RANDOM_ROOT_PASSWORD=yes  mysql/mysql-server 
Unable to find image 'mysql/mysql-server:latest' locally
latest: Pulling from mysql/mysql-server
25f80540fac6: Pull complete 
c7738afc36d4: Pull complete 
91078f446ee3: Pull complete 
5e2d1fbac0aa: Pull complete 
fd68d5d37815: Pull complete 
eabe44e0b828: Pull complete 
7e3e9b32f027: Pull complete 
Digest: sha256:2df31b2e6c787f8ed8c4ef835d59b83b33dcd682f907f92394a2ece2ab7ab5a6
Status: Downloaded newer image for mysql/mysql-server:latest
d657e63b3bad80d64cd31962cd5fdf61bd4b10f5ac5912dd7712596f03de0307

learning_docker on  main [!?] on ☁️  (us-east-1) took 28s 
➜ docker container ls 
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS                             PORTS                                     NAMES
d657e63b3bad   mysql/mysql-server   "/entrypoint.sh mysq…"   16 seconds ago   Up 15 seconds (health: starting)   0.0.0.0:3306->3306/tcp, 33060-33061/tcp   mysql-test
80df0e5e394e   httpd                "httpd-foreground"       2 minutes ago    Up 2 minutes                       0.0.0.0:8080->80/tcp                      httpd-test
4df989e45d99   nginx                "/docker-entrypoint.…"   3 minutes ago    Up 3 minutes                       0.0.0.0:80->80/tcp                        nginx-test

learning_docker on  main [!?] on ☁️  (us-east-1) 
➜ docker container logs --help

Usage:  docker container logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)

learning_docker on  main [!?] on ☁️  (us-east-1) 
➜ docker container logs d6    
[Entrypoint] MySQL Docker Image 8.0.27-1.2.5-server
[Entrypoint] Initializing database
2021-11-11T10:05:15.613553Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.27) initializing of server in progress as process 17
2021-11-11T10:05:15.619310Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-11-11T10:05:15.858696Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-11-11T10:05:16.556054Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1 is enabled for channel mysql_main
2021-11-11T10:05:16.556074Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1.1 is enabled for channel mysql_main
2021-11-11T10:05:16.614451Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
[Entrypoint] Database initialized
2021-11-11T10:05:19.071810Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.27) starting as process 64
2021-11-11T10:05:19.083789Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-11-11T10:05:19.163565Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-11-11T10:05:19.282578Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1 is enabled for channel mysql_main
2021-11-11T10:05:19.282598Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1.1 is enabled for channel mysql_main
2021-11-11T10:05:19.283889Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2021-11-11T10:05:19.283912Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2021-11-11T10:05:19.291487Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2021-11-11T10:05:19.291492Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.27'  socket: '/var/lib/mysql/mysql.sock'  port: 0  MySQL Community Server - GPL.
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
[Entrypoint] GENERATED ROOT PASSWORD: I4eB&B+PUbp^k&r6_84Yv2b+2Rm95@h?

[Entrypoint] ignoring /docker-entrypoint-initdb.d/*

2021-11-11T10:05:20.633899Z 11 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.0.27).
2021-11-11T10:05:21.910104Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.27)  MySQL Community Server - GPL.
[Entrypoint] Server shut down

[Entrypoint] MySQL init process done. Ready for start up.

[Entrypoint] Starting MySQL 8.0.27-1.2.5-server
2021-11-11T10:05:22.813505Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.27) starting as process 1
2021-11-11T10:05:22.820623Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-11-11T10:05:22.893420Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-11-11T10:05:22.962838Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1 is enabled for channel mysql_main
2021-11-11T10:05:22.962855Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1.1 is enabled for channel mysql_main
2021-11-11T10:05:22.963318Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2021-11-11T10:05:22.963338Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2021-11-11T10:05:22.971565Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2021-11-11T10:05:22.971587Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.27'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  MySQL Community Server - GPL.
```
 
 
 
 
 
 

 
 




