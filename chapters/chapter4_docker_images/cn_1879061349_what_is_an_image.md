- cn_1879061349_what_is_an_image
	- Created on the: 2021-11-11 17:38
	- Review date: 2022-11-11
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
		- https://docs.docker.com/engine/reference/commandline/images/
	- Author Notes: 
	- Tags: #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# what is an image?
Requirements ⚓ you have docker installed and have run containers
Learning AIM 🎯 get you up to speed on what an image is.

- app binaries and dependencies
- Metadata about the image data and how to run the image
- Official definition: "An Image is an ordered collection of root filesystem changes and the corresponding execution parameters for use within a container runtime."
- Not a complete OS. No kernel, kernel modules (e.g. drivers)
- Small as one file (your app binary) like a golang static binary
- Big as a Ubuntu distro with apt, and Apache, PHP, and more installed

A Docker image is a file used to execute code in a Docker container. Docker images act as a set of instructions to build a Docker container, like a template. Docker images also act as the starting point when using Docker. An image is comparable to a snapshot in virtual machine (VM) environments.

Docker is used to create, run and deploy applications in containers. A Docker image contains application code, libraries, tools, dependencies and other files needed to make an application run. When a user runs an image, it can become one or many instances of a container.

Docker images have multiple layers, each one originates from the previous layer but is different from it. The layers speed up Docker builds while increasing reusability and decreasing disk use. Image layers are also read-only files. Once a container is created, a writable layer is added on top of the unchangeable images, allowing a user to make changes.

References to disk space in Docker images and containers can be confusing. It's important to distinguish between size and virtual size. Size refers to the disk space that the writable layer of a container uses, while virtual size is the disk space used for the container and the writeable layer. The read-only layers of an image can be shared between any container started from the same image.

### Docker container vs. Docker image

A Docker container is a virtualized runtime environment used in application development. It is used to create, run and deploy applications that are isolated from the underlying hardware. A Docker container can use one machine, share its kernel and virtualize the OS to run more isolated processes. As a result, Docker containers are lightweight.

A Docker image is like a snapshot in other types of VM environments. It is a record of a Docker container at a specific point in time. Docker images are also immutable. While they can't be changed, they can be duplicated, shared or deleted. The feature is useful for testing new software or configurations because whatever happens, the image remains unchanged.

Containers need a runnable image to exist. Containers are dependent on images, because they are used to construct runtime environments and are needed to run an application.

### Anatomy of a Docker image

A Docker image has many layers, and each image includes everything needed to configure a container environment -- system libraries, tools, dependencies and other files. Some of the parts of an image include:

- Base image. The user can build this first layer entirely from scratch with the build command.
- Parent image. As an alternative to a base image, a parent image can be the first layer in a Docker image. It is a reused image that serves as a foundation for all other layers.
- Layers. Layers are added to the base image, using code that will enable it to run in a container. Each layer of a Docker image is viewable under /var/lib/docker/aufs/diff, or via the Docker history command in the command-line interface (CLI). Docker's default status is to show all top-layer images, including repository, tags and file sizes. Intermediate layers are cached, making top layers easier to view. Docker has storage drives that handle the management of image layer contents.
- Container layer. A Docker image not only creates a new container, but also a writable or container layer. This layer hosts changes made to the running container and stores newly written and deleted files, as well as changes to existing files. This layer is also used to customize containers.
- Docker manifest. This part of the Docker image is an additional file. It uses JSON format to describe the image, using information such as image tags and digital signature.

### Docker image repositories

Docker images get stored in private or public repositories, such as those in the Docker Hub cloud registry service, from which users can deploy containers and test and share images. Docker Hub's Docker Trusted Registry also provides image management and access control capabilities.

Official images are ones Docker produces, while community images are images Docker users create. CoScale agent is an official Docker image that monitors Docker applications. Datadog/docker-dd-agent, a Docker container for agents in the Datadog log management program, is an example of a community Docker image.

Users can also create new images from existing ones and use the docker push command to upload custom images to the Docker Hub. To ensure the quality of community images, Docker provides feedback to authors prior to publishing. Once the image is published, the author is responsible for updates. Authors must be cautious when sourcing an image from another party because attackers can gain access to a system through copycat images designed to trick a user into thinking they are from a trusted source.

The concept of a latest image may also cause confusion. Docker images tagged with ":latest" are not necessarily the latest in an ordinary sense. The latest tag does not refer to the most recently pushed version of an image; it is simply a default tag.
How to create a Docker image

Docker images can be created through either an interactive or Dockerfile method.

### Image creation and storage 
to recap images are:
- Created Using a Dockerfile
-  Or committing a containers changes back to an image • Stored in Docker Engine image cache
-  Move images in/out of cache via:
- local filesystem via tarballs
- push/pull to a remote "image registry" (e.g. Docker Hub)
- Images aren't ideal for persistent data
- Mount a host file system path into container
- Use docker volume to create storage for unique/persistent data

