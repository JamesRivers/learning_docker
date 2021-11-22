- cn_1879061871_image_layers
	- Created on the: 2021-11-11 17:38
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- https://docs.docker.com/storage/storagedriver/
	- Author Notes: 
	- Tags: #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
# image layers
Learning AIM 🎯 
 - review image layers
- what is the union file system
- how to review `history` and `inspect` commands 
- copy on write

## history of images
```bash
➜ docker images ls
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
Pick an image and review the `history` 

```bash 
➜ docker image history nginx 
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
f1325989da19   40 hours ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B        
<missing>      40 hours ago   /bin/sh -c #(nop)  STOPSIGNAL SIGQUIT           0B        
<missing>      40 hours ago   /bin/sh -c #(nop)  EXPOSE 80                    0B        
<missing>      40 hours ago   /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B        
<missing>      40 hours ago   /bin/sh -c #(nop) COPY file:09a214a3e07c919a…   4.61kB    
<missing>      40 hours ago   /bin/sh -c #(nop) COPY file:0fd5fca330dcd6a7…   1.04kB    
<missing>      40 hours ago   /bin/sh -c #(nop) COPY file:0b866ff3fc1ef5b0…   1.96kB    
<missing>      40 hours ago   /bin/sh -c #(nop) COPY file:65504f71f5855ca0…   1.2kB     
<missing>      40 hours ago   /bin/sh -c set -x     && addgroup --system -…   60.1MB    
<missing>      40 hours ago   /bin/sh -c #(nop)  ENV PKG_RELEASE=1~bullseye   0B        
<missing>      40 hours ago   /bin/sh -c #(nop)  ENV NJS_VERSION=0.7.0        0B        
<missing>      40 hours ago   /bin/sh -c #(nop)  ENV NGINX_VERSION=1.21.4     0B        
<missing>      40 hours ago   /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B        
<missing>      4 weeks ago    /bin/sh -c #(nop)  CMD ["bash"]                 0B        
<missing>      4 weeks ago    /bin/sh -c #(nop) ADD file:d84144ad575672cd6…   74.3MB  
```

A hisgtory of the image layers. 

Base layer is known as `scratch`, changes are then added on top. 

This Dockerfile contains four commands. Commands that modify the filesystem create a layer. TheFROM statement starts out by creating a layer from the ubuntu:18.04 image. The LABEL command only modifies the image’s metadata, and does not produce a new layer. The COPY command adds some files from your Docker client’s current directory. The first RUN command builds your application using the make command, and writes the result to a new layer. The second RUN command removes a cache directory, and writes the result to a new layer. Finally, the CMD instruction specifies what command to run within the container, which only modifies the image’s metadata, which does not produce an image layer.

Each layer is only a set of differences from the layer before it. Note that both adding, and removing files will result in a new layer. In the example above, the $HOME/.cache directory is removed, but will still be available in the previous layer and add up to the image’s total size. Refer to the Best practices for writing Dockerfiles and use multi-stage builds sections to learn how to optimize your Dockerfiles for efficient images.

The layers are stacked on top of each other. When you create a new container, you add a new writable layer on top of the underlying layers. This layer is often called the “container layer”. All changes made to the running container, such as writing new files, modifying existing files, and deleting files, are written to this thin writable container layer. The diagram below shows a container based on an ubuntu:15.04 image.

![](../../attachments/Pasted%20image%2020211111180721.png)

to recap 
 - Images are made up of file system changes and metadata
- Each layer is uniquely identified and only stored once on a host
-  This saves storage space on host and transfer time on push/pull
-  A container is just a single read/write layer on top of image
-  docker image history and inspect commands can teach us
