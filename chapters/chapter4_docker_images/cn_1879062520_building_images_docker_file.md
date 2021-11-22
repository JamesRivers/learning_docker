- cn_1879062520_building_images_docker_file
	- Created on the: 2021-11-11 17:38
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- https://docs.docker.com/engine/reference/builder/
		- https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
	- Author Notes: 
	- Tags: #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.

# docker file
Requirements ⚓ 
- Know what container and images are 
- Understand image layer basics
- Understand Docker Hub basics

Learning AIM 🎯 to get you up to speed on docker file and how to use it

## tags
Exercise 🤸  new command  `docker image tag --help`
```bash
➜ docker image tag --help

Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```

focus on the repo column  and understand the repo names, official or not? 

```bash
➜ docker image ls             
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
nginx                 latest    f1325989da19   40 hours ago    134MB
httpd                 latest    5ee17b7e9c90   2 weeks ago     136MB
mongo                 latest    addfd455919f   3 weeks ago     654MB
ubuntu                latest    d5ca7a445605   3 weeks ago     65.6MB
amazon/aws-cli        latest    c204b6883903   6 weeks ago     393MB
hashicorp/terraform   latest    9a5f4cb4589a   8 weeks ago     104MB
centos                latest    e6a0117ec169   8 weeks ago     272MB
alpine                latest    bb3de5531c18   2 months ago    5.34MB
ubuntu                <none>    8707eaf21636   4 months ago    65.6MB
rwxrob/lynx           latest    9f3aa8971b36   7 months ago    152MB
centos                7         e5df02c43685   12 months ago   301MB
```

Now the tags, pick on nginx. we are using `latest`. But on docker hub what do we have - https://hub.docker.com/_/nginx?tab=tags alot !

But what happens if we pull the nginx with the tag mainline? 

```bash
❯ docker pull nginx:mainline
mainline: Pulling from library/nginx
Digest: sha256:dfef797ddddfc01645503cef9036369f03ae920cac82d344d58b637ee861fda1
Status: Downloaded newer image for nginx:mainline
docker.io/library/nginx:mainline

learning_docker/reference on  main [!?] on ☁️  (us-east-1) took 2s 
➜ docker image ls           
REPOSITORY            TAG        IMAGE ID       CREATED         SIZE
nginx                 latest     f1325989da19   40 hours ago    134MB
nginx                 mainline   f1325989da19   40 hours ago    134MB
```

We have 2 images but it is the same `image id`. This is the same image. 

### retag existing images 
you can retag existing images in the local cache if you need to. 
```bash
docker image tag nginx jamesrivers/nginx
docker image ls                         
REPOSITORY            TAG        IMAGE ID       CREATED         SIZE
jamesrivers/nginx     latest     f1325989da19   41 hours ago    134MB
nginx                 latest     f1325989da19   41 hours ago    134MB
```
It is just a label...  I have added a new repo. But this is just a local update on my local docker engine here.  What I can do is push this to my docker hub repo. Do that I really should put my actual repo name in the tag... 

### docker push 
We are now going to push this to out docker hub repo. Here are the steps:
- docker image push rivers1980/nginx

Done !
If  you get an error make sure that you have logged into docker hub and or any other private repo.  Private keys are stored in a conf file, it differs in location on mac, linux and windows. 

### additional tags 
remove the `latest` tag and put something else... 
```bash 
docker image tag nginx rivers1980/nginx:test
➜ docker image ls                             
REPOSITORY            TAG        IMAGE ID       CREATED         SIZE
nginx                 latest     f1325989da19   41 hours ago    134MB
nginx                 mainline   f1325989da19   41 hours ago    134MB
rivers1980/nginx      latest     f1325989da19   41 hours ago    134MB
rivers1980/nginx      test       f1325989da19   41 hours ago    134MB
```

push again
```bash
➜ docker image push rivers1980/nginx:test     
The push refers to repository [docker.io/rivers1980/nginx]
30f1e00dbe24: Layer already exists 
7bbc7b4ec9ea: Layer already exists 
edcce1d38c0e: Layer already exists 
a8198c480a98: Layer already exists 
1f28dffc7992: Layer already exists 
18acdb3e3c0d: Layer already exists 
test: digest: sha256:60da4710dc9cda1e59f3d0f1b02cfb48f87ce7f2b6e8024403f717da5ab52f12 size: 1570
```

![](../../attachments/Pasted%20image%2020211111183633.png)

to recap:
- Properly tagging images
- tagging images for upload to Docker Hub • How tagging is related to image ID
- The Latest Tag
- Logging into Docker Hub from docker cli
- How to create private Docker Hub images


latest is not latest remember that...

## Docker File Walkthrough
We will work on this example :
```bash
#       describes
FROM debian:stretch-slim
# all images must have a FROM
# usually from a minimal Linux distribution like debian or (even better) alpine
# if you truly want to start with an empty container, use FROM scratch

ENV NGINX_VERSION 1.13.6-1~stretch
ENV NJS_VERSION   1.13.6.0.1.14-1~stretch
# optional environment variable that's used in later lines and set as envvar when container is running

RUN apt-get update \
        && apt-get install --no-install-recommends --no-install-suggests -y gnupg1 \
        && \
        NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; \
        found=''; \
        for server in \
                ha.pool.sks-keyservers.net \
                hkp://keyserver.ubuntu.com:80 \
                hkp://p80.pool.sks-keyservers.net:80 \
                pgp.mit.edu \
        ; do \
                echo "Fetching GPG key $NGINX_GPGKEY from $server"; \
                apt-key adv --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY" && found=yes && break; \
        done; \
        test -z "$found" && echo >&2 "error: failed to fetch GPG key $NGINX_GPGKEY" && exit 1; \
        apt-get remove --purge -y gnupg1 && apt-get -y --purge autoremove && rm -rf /var/lib/apt/lists/* \
        && echo "deb http://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list \
        && apt-get update \
        && apt-get install --no-install-recommends --no-install-suggests -y \
                                                nginx=${NGINX_VERSION} \
                                                nginx-module-xslt=${NGINX_VERSION} \
                                                nginx-module-geoip=${NGINX_VERSION} \
                                                nginx-module-image-filter=${NGINX_VERSION} \
                                                nginx-module-njs=${NJS_VERSION} \
                                                gettext-base \
        && rm -rf /var/lib/apt/lists/*
# optional commands to run at shell inside container at build time
# this one adds package repo for nginx from nginx.org and installs it

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
        && ln -sf /dev/stderr /var/log/nginx/error.log
# forward request and error logs to docker log collector

EXPOSE 80 443
# expose these ports on the docker virtual network
# you still need to use -p or -P to open/forward these ports on host

CMD ["nginx", "-g", "daemon off;"]
# required: run this command when container is launched
# only one CMD allowed, so if there are multiple, last one wins
```

- The `FROM` stanza, the first and a must .. what is base image you you are going to build upon
- The `ENV` stanza, environmental variables, in this example we are declaring the `nginx` version that we are going to use. 

> Note that each stanza in this docker file is a layer that we are adding to the image.  So the order matters - top down....

-  The `RUN` commands  - execueting commands inside container

> Note the practice of the logs, pointing the log files to the `stdout` and `stderror` ... remember Docker handles all the logs for us, so we need to point our container logs to Docker and shoot.  More about logs and logging drivers later. 

- The `EXPOSE` stanza, right we need to get the container to listen on the ports required. No TCP or UDP ports are opened on a container by default - we need to do it.  The ports on the host still needs to have the ports published later, that is the `-p` option we use in `docker run` to get the ports from host to container.  But is the container listening?  
- The `CMD` stanza ... last item the final command that the the container will run when started. 

Great resource to review is the docs.docker https://docs.docker.com/engine/reference/builder/

Ok so we have covered the 5 basic stanzas.... 







