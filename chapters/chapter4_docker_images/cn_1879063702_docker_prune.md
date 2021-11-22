- cn_1879063702_docker_prune
	- Created on the: 2021-11-22 16:41
	- Review date:
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
	- Sources: 
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# Docker prune... Clean up!
You can use "prune" commands to clean up images, volumes, build cache, and containers. Examples include:

- `docker image prune` to clean up just "dangling" images
```bash
❯ docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y
Deleted Images:
deleted: sha256:8de53258fba8e6a1311b258b5008cad249854c2d0edda8145f4bb2ba32b8445e
untagged: ubuntu@sha256:aba80b77e27148d99c034a987e7da3a287ed455390352663418c0f2ed40417fe
deleted: sha256:8707eaf216364b381d33fb63c3d9ee533c4c783fb0eee0282fa6f35386a0366f
deleted: sha256:edefbcfd02e5c83fe0bf0e78acbfbccd7ef9284cf68c93d460095174c2c76555
deleted: sha256:f3aae66595b14707901f42ae841b6b3909aad7a7db5dd66511b08bb6b8db3206

Total reclaimed space: 65.55MB
```

- `docker system prune` will clean up everything
```bash
➜ docker system prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N] y
```

- The big one is usually `docker image prune -a` which will remove all images you're not using. Use `docker system df` to see space usage.

Remember each one of those commands has options you can learn with `--help`.

Here's a YouTube video Brett made about it: [https://youtu.be/_4QzP7uwtvI](https://youtu.be/_4QzP7uwtvI)

Lastly, realize that if you're using Docker Toolbox, the Linux VM won't auto-shrink. You'll need to delete it and re-create (make sure anything in docker containers or volumes are backed up). You can recreate the toolbox default VM with `docker-machine rm default` and then `docker-machine create`
