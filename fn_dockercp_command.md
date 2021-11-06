- fn_dockercp_command
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-07-23 12:53
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://docs.docker.com/engine/reference/commandline/cp/	
	- Author Notes: 
	- Tags: 
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---

In order to copy a file from a container to the host, you can use the command

```
docker cp <containerId>:/file/path/within/container /host/path/target
```

The `docker cp` utility copies the contents of `SRC_PATH` to the `DEST_PATH`. You can copy from the container’s file system to the local machine or the reverse, from the local filesystem to the container. If `-` is specified for either the `SRC_PATH` or `DEST_PATH`, you can also stream a tar archive from `STDIN` or to `STDOUT`. The `CONTAINER` can be a running or stopped container. The `SRC_PATH` or `DEST_PATH` can be a file or directory.