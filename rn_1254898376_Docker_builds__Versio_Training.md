- 1254898376_Docker_builds__Versio_Training
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-04-30 11:49
	- Created by: James Rivers
	- Written against (version): 
		- VIC 1.5.7
	- Sources: 
		- [VIC](https://bintray.com/vmware/vic)
		- [Integrated Containers](https://www.altaro.com/vmware/a-first-look-at-vsphere-integrated-containers-and-how-to-deploy-them/)
		- [VCH deployment VMWare](https://vmware.github.io/vic-product/assets/files/html/1.4/vic_vsphere_admin/deploy_vch.html)
		- [Configure the Docker Client to work with VIC](https://vmware.github.io/vic-product/assets/files/html/1.4/vic_app_dev/configure_docker_client.html)
		- [Confluence Page Deploy Imagine Dockers ](https://imaginecommunications.atlassian.net/wiki/spaces/DISTP/pages/1590384711/deploy.imaginecommunications.com%2B443%2BDocker%2BRegistry%2BSetup)
	- Author Notes: 
	- Tags: [!Versio Platform MOC](playout/versio/!Versio%20Platform%20MOC.md)

	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
***
## What are we doing? 
Building out a Versio HA environment for testing and training purposes with the help of Docker. Plus we I am looling at VMware VIC to incorparate Docker into my existing VMWare deployment on premise

## What will this look like? 
that is a great question and is one that I going to have to add to as I build out this page... 

### What am I trying to solve?
I have a VMWare Hyper-V set up in the on premise solution. 
I want to use Docker containers to run various services - rabbit, postgres, core services etc. 

Should I build a guest VM hosting CentOS7 or debian etc then build my docker containers on top of that? 

Or should I look at VMWare VIC? VIC - [check VIC here](https://vmware.github.io/vic-product/assets/files/html/1.3/)

So in this rn I am building a VMWare VIC and using Docker and I will review the options and states of this as an option.  Learning ... 


### What is Docker?
Just in case you didn't know, and if you didn't where have you been???

Docker is an open-source containerization framework. Containers are created using base images residing on what’s called a Docker Hub, which is a registry or repository of Docker images. A Docker client (command line tool) is used to connect to a Docker daemon or endpoint running on the container host. A Docker environment can be set up locally on Linux, Windows, Mac and VMware’s Photon OS or, if preferred, on a cloud platform such as Amazon’s AWS or Microsoft’s Azure. If you’re new to Docker, like I am, the best source of information is Docker’s own [documentation website](https://docs.docker.com/). There you’ll find guides, tutorials and examples which should get you going in no time at all. 

more notes [Docker_Start_Here](Docker_Start_Here.md)

### Does VMWare compete in this space? 
VMware has very recently added vSphere Integrated Containers (VIC) to its line of features.  VIC give a new twist to the overall concept since containers are deployed as virtual machines as opposed to OS slices. The virtualized container host (VCH), which as you’ve guessed comes virtualized, provides the same Docker endpoints which you’d normally connect to using traditional Docker clients.  So why did VMware go down this route? Paraphrasing, there are a number advantages to this approach:

vSphere, not Linux, is the container host:

-   Containers are deployed as VMs, not in VMs.
-   Every container is fully isolated from the host and from the other containers.
-   vSphere provides per-tenant dynamic resource limits within a vCenter Server cluster.

vSphere, not Linux, is the infrastructure:

-   You can select vSphere networks that appear in the Docker client as container networks.
-   Images, volumes, and container state are provisioned directly to VMFS.

vSphere is the control plane:

-   Use the Docker client to directly control selected elements of vSphere infrastructure.
-   A container endpoint Service-as-a-Service presents as a service abstraction, not as IaaS.

In addition, VIC is comprised of the following components (source: VMware):

-   vSphere Integrated Containers Engine: A container engine that is designed to integrate of all the packaging and runtime benefits of containers with the enterprise capabilities of your vSphere environment.
-   vSphere Integrated Containers Registry: A Docker image registry with additional capabilities such as role-based access control (RBAC), replication, and so on.

I suggest reading this guide for further details on the architecture, use case examples and on how to deploy VIC. The [VMware VIC documentation site](https://www.vmware.com/products/vsphere/integrated-containers.html) is also a good source of information.

Download the VMWARE VIC [here](https://my.vmware.com/en/web/vmware/downloads/details?downloadGroup=VIC157&productId=843&rPId=64257)


### Home LAB set up for Docker and VMWare VIC
We have a VMWare server at 192.168.1.99
We have donwloaded the VIC software and it is hosted on the NAS at
> \F. Software\VMWare VIC\vic

We have a Win 10 PC - wyc-pc1 on the network - 192.168.1.66

From the windows 10 pc we are going to create a VCH on the VMWare server. 

What is a VCH? - In vSphere Integrated Containers, you deploy virtual container hosts (VCHs) that serve as Docker API endpoints. VCHs allow Docker developers to provision containers as VMs in your vSphere environment. For a description of the role and function of VCHs, see [Introduction to vSphere Integrated Containers Engine](https://vmware.github.io/vic-product/assets/files/html/1.4/vic_overview/intro_to_vic_engine.html).

To review the deployment of the VCH in the home lab visit [rn_VMWasre_VIC_VCH_Deployment_with_Docker](rn_VMWasre_VIC_VCH_Deployment_with_Docker.md) to review.

### Deployment of Versio Components using Docker

First we need to test connectivity from the Docker Client to the Imagine Communications repo - artifactory

>  docker login -u name -p password deploy.imaginecommunications.com:443

Test pull of package - IC-RABBITMQ
> ![](/attachments/Pasted%20image%2020210507143357.png)

```bash
\[root@dockerclient ~\]# docker -H 192.168.1.152:2375 pull deploy.imaginecommunications.com:443/versio-docker-dev/ic-rabbitmq:3.8.9-standalone-1.1

3.8.9-standalone-1.1: Pulling from versio-docker-dev/ic-rabbitmq
df20fa9351a1: Pull complete 
a3ed95caeb02: Extracting \[==================================================>\] 2B/32B
6a4ed0140701: Pull complete 
f7089526c216: Pull complete 
1407d26607ae: Pull complete 
3e3c63102fb6: Pull complete 
ecc612aa631d: Pull complete 
5eaa934021ba: Pull complete 
199bd54a6183: Verifying Checksum 
0ab1e0167db5: Download complete 
bfd179911d9a: Download complete 
f18b453a7aad: Download complete 
3ea93191a7fc: Download complete 
85c095883e4d: Download complete 
ca402
```
![](/attachments/Pasted%20image%2020210507143631.png)

Deployment of a Rabbit Server - Test 
```
docker run -d -p 15672:15672 deploy.imaginecommunications.com:443/versio-docker-dev/ic-rabbitmq:3.8.9-standalone-1.1
```

show running container
```bash
\[root@dockerclient /\]# docker ps -a

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES

b22b6f74c885 deploy.imaginecommunications.com:443/versio-docker-dev/ic-rabbitmq:3.8.9-standalone-1.1 "docker-entrypoint.s…" About a minute ago Up About a minute 192.168.1.152:15672->15672/tcp cocky\_austin

\[root@dockerclient /\]#
```

Access the Rabbit UI

![](/attachments/Pasted%20image%2020210507160929.png)

rabbit container running and seen in the VMWare UI. 

![](/attachments/Pasted%20image%2020210507161045.png)

docker images are hosted where?
- The **docker images**, they are stored inside the **docker** directory: /var/lib/**docker**/ **images**
- But on a VMWare VIC where are they stored? - Still a question that i need to review and fins the answer