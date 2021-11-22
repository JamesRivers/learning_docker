- cn_1879062638_running_docker_builds
	- Created on the: 2021-11-22 13:12
	- Review date:
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- https://docs.docker.com/get-started/02_our_app/
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# running docker builds 
Take the following `Dockerfile` 
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
## Application build with docker file

In this next section I am going use a differing docker file and an application build. 

Before we can run the application, we need to get the application source code onto our machine. For real projects, you will typically clone the repo. But, for this tutorial, we have created a ZIP file containing the application.

1.  [Download the App contents](https://github.com/docker/getting-started/tree/master/app). You can either pull the entire project or download it as a zip and extract the app folder out to get started with
    
2.  Once extracted, use your favorite code editor to open the project. If you’re in need of an editor, you can use [Visual Studio Code](https://code.visualstudio.com/). You should see the `package.json` and two subdirectories (`src` and `spec`).

![](../../attachments/Pasted%20image%2020211122134816.png)

In order to build the application, we need to use a `Dockerfile`. A Dockerfile is simply a text-based script of instructions that is used to create a container image. If you’ve created Dockerfiles before, you might see a few flaws in the Dockerfile below. But, don’t worry. We’ll go over them.

1.  Create a file named `Dockerfile` in the same folder as the file `package.json` with the following contents.


```bash
# syntax=docker/dockerfile:1
FROM node:12-alpine
RUN apk add --no-cache python3 g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```

-   Please check that the file `Dockerfile` has no file extension like `.txt`. Some editors may append this file extension automatically and this would result in an error in the next step.
    
-   If you haven’t already done so, open a terminal and go to the `app` directory with the `Dockerfile`. Now build the container image using the `docker build` command.


```bash
docker image build -t getstarted . --platform linux/amd64
```

What are we doing here, first we are instructing `Docker`to create a new `image` with the `tag` testnginx and I am asking for it to be built in this current directory. 

This command used the Dockerfile to build a new container image. You might have noticed that a lot of “layers” were downloaded. This is because we instructed the builder that we wanted to start from the `node:12-alpine` image. But, since we didn’t have that on our machine, that image needed to be downloaded.

After the image was downloaded, we copied in our application and used `yarn` to install our application’s dependencies. The `CMD` directive specifies the default command to run when starting a container from this image.

Finally, the `-t` flag tags our image. Think of this simply as a human-readable name for the final image. Since we named the image `getstarted`, we can refer to that image when we run a container.

The `.` at the end of the `docker build` command tells that Docker should look for the `Dockerfile` in the current directory.

> Note if you are building on an `arm` based architecture, like the m1 chip as an example, then you need to include the `--platform linux/amd64` option, to run an Intel image under emulation.
> In summary, running Intel-based containers on Arm-based machines should be regarded as “best effort” only. We recommend running arm64 containers on Apple Silicon machines whenever possible, and encouraging container authors to produce arm64, or multi-arch, versions of their containers. We expect this issue to become less common over time, as more and more images are rebuilt [supporting multiple architectures](https://www.docker.com/blog/multi-arch-build-and-images-the-simple-way/).

```bash
❯ docker image build -t getstarted . --platform linux/amd64
[+] Building 49.4s (14/14) FINISHED                                                                               
 => [internal] load build definition from Dockerfile                                                         0.0s
 => => transferring dockerfile: 37B                                                                          0.0s
 => [internal] load .dockerignore                                                                            0.0s
 => => transferring context: 2B                                                                              0.0s
 => resolve image config for docker.io/docker/dockerfile:1                                                   0.6s
 => CACHED docker-image://docker.io/docker/dockerfile:1@sha256:42399d4635eddd7a9b8a24be879d2f9a930d0ed040a6  0.0s
 => [internal] load build definition from Dockerfile                                                         0.0s
 => [internal] load .dockerignore                                                                            0.0s
 => [internal] load metadata for docker.io/library/node:12-alpine                                            0.9s
 => [1/5] FROM docker.io/library/node:12-alpine@sha256:0eca266c5fe38ba93aebac00e45c9ac1bb7328b0702a6dc10e1a  5.0s
 => => resolve docker.io/library/node:12-alpine@sha256:0eca266c5fe38ba93aebac00e45c9ac1bb7328b0702a6dc10e1a  0.0s
 => => sha256:0eca266c5fe38ba93aebac00e45c9ac1bb7328b0702a6dc10e1a6ea543d49301 1.43kB / 1.43kB               0.0s
 => => sha256:3a8ee0d3482bc9139a3554821936379cac9b4a01135ca34ddcf6505b6631ce12 1.16kB / 1.16kB               0.0s
 => => sha256:2f014773d54a76e62b0b38b3c564e7665f56b806b202353c4f094b47f4d8737b 6.53kB / 6.53kB               0.0s
 => => sha256:97518928ae5f3d52d4164b314a7e73654eb686ecd8aafa0b79acd980773a740d 2.82MB / 2.82MB               0.6s
 => => sha256:468000513d90bb27c6602b0b3bb5b7478173e555d9b905e768265694ad5c0192 24.77MB / 24.77MB             4.0s
 => => sha256:7e4d2470b55737d054727607cda782e47ae7c2f3bdbf619d888d110943bb9278 2.37MB / 2.37MB               1.2s
 => => extracting sha256:97518928ae5f3d52d4164b314a7e73654eb686ecd8aafa0b79acd980773a740d                    0.1s
 => => sha256:26993460ce451dc85a494f9cfe75066895482fb4a53b23b40b99289548e96cb4 449B / 449B                   0.7s
 => => extracting sha256:468000513d90bb27c6602b0b3bb5b7478173e555d9b905e768265694ad5c0192                    0.8s
 => => extracting sha256:7e4d2470b55737d054727607cda782e47ae7c2f3bdbf619d888d110943bb9278                    0.1s
 => => extracting sha256:26993460ce451dc85a494f9cfe75066895482fb4a53b23b40b99289548e96cb4                    0.0s
 => [internal] load build context                                                                            0.0s
 => => transferring context: 2.49kB                                                                          0.0s
 => [2/5] RUN apk add --no-cache python3 g++ make                                                           11.6s
 => [3/5] WORKDIR /app                                                                                       0.0s
 => [4/5] COPY . .                                                                                           0.0s
 => [5/5] RUN yarn install --production                                                                     30.0s
 => exporting to image                                                                                       1.0s
 => => exporting layers                                                                                      1.0s
 => => writing image sha256:1198c7590915b0d309b37cc21b3da79056ec94972511bb5b19c5db544e71b312                 0.0s
 => => naming to docker.io/library/getstarted                                                                0.0s
 ```
 
 Check the image ls - `docker image ls` there it is. 
 ```bash
 getting-started/app on  master [?] via  on ☁️  (us-east-1) 
❯ docker image ls
REPOSITORY            TAG        IMAGE ID       CREATED         SIZE
getstarted            latest     1198c7590915   2 minutes ago   411MB
```

### Start the app
Now that we have an image, let’s run the application. To do so, we will use the docker run command (remember that from earlier?).

    Start your container using the docker run command and specify the name of the image we just created:

 `docker run -dp 3000:3000 getstarted`

Remember the -d and -p flags? We’re running the new container in “detached” mode (in the background) and creating a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, we wouldn’t be able to access the application.

After a few seconds, open your web browser to http://localhost:3000. You should see our app.

in my case this is 
```bash
getting-started/app on  master [?] via  on ☁️  (us-east-1) 
➜ docker run -dp 3000:3000 --platform linux/amd64 getstarted 
c120abeb79130ed81b97d6b98db4acd429d04c9becac6b321f2199b7a6e9f6fb
````

we now have the todo app.... 
![](../../attachments/Pasted%20image%2020211122141458.png)