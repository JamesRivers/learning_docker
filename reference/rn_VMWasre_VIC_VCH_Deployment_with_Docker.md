- rn_VMWasre_VIC_VCH_Deployment
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-05-07 11:21
	- Created by: James Rivers
	- Written against (version): 1.5.7 VIC
	- Sources: 
		- https://vmware.github.io/vic-product/assets/files/html/1.5/vic_vsphere_admin/installing_vic.html
		- https://docs.vmware.com/en/VMware-vSphere-Integrated-Containers/index.html
		- 
	- Author Notes: 
		- Attempting to host containers on a VMWare ESXi Server and link them together and use with versio playout instances etc... 
	- Tags: [!Versio_Platform_MOC](playout/versio/!Versio_Platform_MOC.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
***
# vSphere Integrated Containers (VIC) Overview 
vSphere Integrated Containers enables IT teams to seamlessly run traditional workloads and container workloads side-by-side on existing vSphere infrastructure.

The solution is delivered as an appliance, that comprises the following major components:

-   **vSphere Integrated Containers Engine**, a container runtime for vSphere that allows you to provision containers as virtual machines, offering the same security and functionality of virtual machines in VMware ESXi™ hosts or vCenter Server® instances.
-   **vSphere Integrated Containers Plug-In for vSphere Client**, that provides information about your vSphere Integrated Containers setup and allows you to deploy virtual container hosts directly from the vSphere Client.
-   **vSphere Integrated Containers Registry (Harbor)**, an enterprise-class container registry server that stores and distributes container images. vSphere Integrated Containers Registry extends the [Docker Distribution](https://github.com/docker/distribution) open source project by adding the functionalities that an enterprise requires, such as security, identity and management.
-   **vSphere Integrated Containers Management Portal**, a container management portal, built on the VMware Admiral project, that provides a UI for DevOps teams to provision and manage containers, including the ability to obtain statistics and information about container instances. Management Portal administrators can manage container hosts and apply governance to their usage, including capacity quotas and approval workflows. Management Portal administrators can create projects, and assign users and resources such as registries and virtual container hosts to those projects.

All components run on Photon OS 2.0. These components currently support the Docker image format. vSphere Integrated Containers is entirely Open Source and free to use. vSphere Integrated Containers depends on certain features that are included in the following vSphere Editions:

-   vSphere Enterprise Plus
-   vSphere Remote Office Branch Office (ROBO) Advanced

For a walkthrough of vSphere Integrated Containers deployment, configuration and operation, watch the vSphere Integrated Containers overview video.

https://youtu.be/phsVFTVK4t4

## Advantages of vSphere Integrated Containers

vSphere Integrated Containers is designed to solve many of the challenges associated with putting containerized applications into production. It directly uses the clustering, dynamic scheduling, and virtualized infrastructure in vSphere and bypasses the need to maintain discrete Linux VMs as container hosts.

vSphere Integrated Containers allows the vSphere administrator to provide a container management endpoint to a user as a service. At the same time, the vSphere administrator remains in complete control over the infrastructure that the container management endpoint service depends on. The main differences between vSphere Integrated Containers and a classic container environment are the following:

-   **vSphere, not Linux, is the container host**
    -   Containers are deployed _as_ VMs, not _in_ VMs.
    -   Every container is fully isolated from the host and from the other containers.
    -   vSphere provides per-tenant dynamic resource limits within a vCenter Server cluster.
-   **vSphere, not Linux, is the infrastructure**
    -   You can select vSphere networks that appear in the Docker client as container networks.
    -   Images, volumes, and container state are provisioned directly to VMFS.
-   **vSphere is the control plane**
    -   Use the Docker client to directly control selected elements of vSphere infrastructure.
    -   A container endpoint Service-as-a-Service presents as a service abstraction, not as IaaS.

vSphere Integrated Containers is designed to be the fastest and easiest way to provision any Linux-based workload to vSphere, if that workload can be serialized as a Docker image.

## How vSphere Integrated Containers Helps vSphere Administrators

vSphere Integrated Containers gives the vSphere administrator the tools to easily make the vSphere infrastructure accessible to users so that they can provision container workloads into production.

**Scenario 1: A Classic Container Environment**

In a classic container environment:

-   A user raises a ticket and says, "I need Docker".
-   The vSphere administrator provisions a large Linux VM and sends them the IP address.
-   The user installs Docker, patches the OS, configures in-guest network and storage virtualization, secures the guest, isolates the containers, packages the containers efficiently, and manages upgrades and downtime.

In this scenario, what the vSphere administrator has provided is similar to a nested hypervisor, that is opaque and that they have to manage. If they scale that up to one large Linux VM per tenant, they end up creating a large distributed silo for containers.

**Scenario 2: vSphere Integrated Containers**

With vSphere Integrated Containers:

-   A user raises a ticket and says, "I need Docker".
-   The vSphere administrator identifies datastores, networking, and compute resources on a cluster that users can use for their Docker environment.
-   The vSphere administrator uses the vSphere Integrated Containers plug-in for the vSphere Client or a command-line utility called `vic-machine` to install a small appliance, called a virtual container host (VCH). The VCH represents an authorization to use the infrastructure that they have identified, into which users can self-provision container workloads.
-   The appliance runs a secure remote Docker API, that is the only access that the user has to the vSphere infrastructure.
-   Instead of sending the user a Linux VM, the vSphere administrator sends them the IP address of the VCH, the port of the remote Docker API, and a certificate for secure access.

In this scenario, the vSphere administrator has provided the user with a service portal. This is better for the user because they do not have to worry about isolation, patching, security, backup, and so on. It is better for the vSphere administrator because every container that the user deploys is a container VM. vSphere administrators can perform vMotion and monitor container VMs just like all of their other VMs.

If the user needs more compute capacity, in Scenario 1, the pragmatic choice is to power down the VM and reconfigure it, or give the user a new VM and let them deal with the clustering implications. Both of these solutions are disruptive to users. With vSphere Integrated Containers in Scenario 2, the vSphere administrator can reconfigure the VCH in vSphere, or redeploy it with a new configuration in a way that is completely transparent to the user.

vSphere Integrated Containers allows the vSphere administrator to select and dictate the appropriate infrastructure for the task in hand:

-   **Networking**: Select multiple port groups for different types of network traffic, ensuring that all of the containers that a user provisions get the appropriate interfaces on the right networks.
-   **Storage**: Select different vSphere datastores for different types of state. For example, container state is ephemeral and is unlikely to need to be backed up, but volume state almost certainly should be backed up. vSphere Integrated Containers automatically ensures that state gets written to the appropriate datastore when the user provisions a container.

To summarize, vSphere Integrated Containers gives vSphere administrators a mechanism that allows users to self-provision VMs as containers into the virtual infrastructure.

## Install Steps 
take the VIC 1.8 engine bundle and create a new VCH - Virtual Container Host on the ESXi Server. 

Note I am using a standalone ESXi server, not vCenter or vSphere etc

From the Windows Server run the VIC Machine `create`

```
.\vic-machine-windows.exe create -t 192.168.1.99 -u root -p Imagin3! --no-tls --thumbprint 41:28:C4:9A:D7:52:C9:0D:5F:19:8F:98:B1:7A:4A:A4:08:B6:08:8B --image-store DISK1_SSD_480
```

Additional - I need to create a volume also otherwise the containers will not run. - No volume store named (default) exists.  See [LINK](https://vmware.github.io/vic-product/assets/files/html/1.1/vic_app_dev/ts_volume_store_error.html) 

and this [LINK](https://vmware.github.io/vic-product/assets/files/html/1.5/vic_vsphere_admin/volume_stores.html)



```
PS X:\F. Software\VMWare VIC\VIC1.5.7\vic> .\vic-machine-windows.exe create -t 192.168.1.99 -u root -p Imagin3! --no-tls --thumbprint 41:28:C4:9A:D7:52:C9:0D:5F:19:8F:98:B1:7A:4A:A4:08:B6:08:8B --image-store DISK1_SSD_480 --volume-store DISK1_SSD_480:default
```

![](/attachments/Pasted%20image%2020210507160206.png)


here we can see the container held within


![](/attachments/Pasted%20image%2020210507160611.png)

This will run and create a new VCH on the target server

```
time="2021-05-07T13:33:56+01:00" level=info msg="### Installing VCH ####"
time="2021-05-07T13:33:56+01:00" level=warning msg="Using administrative user for VCH operation - use --ops-user to improve security (see -x for advanced help)"
time="2021-05-07T13:33:56+01:00" level=warning msg="Configuring without TLS - all communications will be insecure"
time="2021-05-07T13:33:56+01:00" level=info msg="Validating supplied configuration"
time="2021-05-07T13:33:56+01:00" level=info msg="Firewall status: ENABLED on \"/ha-datacenter/host/wyc-vm.Home/wyc-vm.Home\""
time="2021-05-07T13:33:56+01:00" level=warning msg="Firewall configuration on \"/ha-datacenter/host/wyc-vm.Home/wyc-vm.Home\" may prevent connection on dst 2377/tcp outbound with allowed IPs: [192.168.1.69]"
time="2021-05-07T13:33:56+01:00" level=warning msg="Unable to fully verify firewall configuration due to DHCP use on management network"
time="2021-05-07T13:33:56+01:00" level=warning msg="VCH management interface IP assigned by DHCP must be permitted by allowed IP settings"
time="2021-05-07T13:33:56+01:00" level=warning msg="Firewall allowed IP configuration may prevent required connection on hosts:"
time="2021-05-07T13:33:56+01:00" level=warning msg="\t\"/ha-datacenter/host/wyc-vm.Home/wyc-vm.Home\""
time="2021-05-07T13:33:56+01:00" level=info msg="Firewall must permit dst 2377/tcp outbound to the VCH management interface"
time="2021-05-07T13:33:56+01:00" level=info msg="License check OK"
time="2021-05-07T13:33:56+01:00" level=info
time="2021-05-07T13:33:57+01:00" level=info msg="Creating appliance on target"
time="2021-05-07T13:33:57+01:00" level=info msg="Network role \"client\" is sharing NIC with \"public\""
time="2021-05-07T13:33:57+01:00" level=info msg="Network role \"management\" is sharing NIC with \"public\""
time="2021-05-07T13:33:57+01:00" level=info msg="Creating the VCH VM"
time="2021-05-07T13:33:57+01:00" level=info msg="Uploading ISO images"
time="2021-05-07T13:33:57+01:00" level=info msg="Uploading appliance.iso as V1.5.7-21381-BA7A5B4B-appliance.iso"
time="2021-05-07T13:34:02+01:00" level=info msg="Uploading bootstrap.iso as V1.5.7-21381-BA7A5B4B-bootstrap.iso"
time="2021-05-07T13:34:04+01:00" level=info msg="Waiting for IP information"
time="2021-05-07T13:34:09+01:00" level=info msg="Waiting for major appliance components to launch"
time="2021-05-07T13:34:09+01:00" level=info msg="Obtained IP address for client interface: \"192.168.1.152\""
time="2021-05-07T13:34:09+01:00" level=info msg="Checking VCH connectivity with vSphere target"
time="2021-05-07T13:34:09+01:00" level=info msg="vSphere API Test: https://192.168.1.99 vSphere API target responds as expected"
time="2021-05-07T13:34:13+01:00" level=info msg="Initialization of appliance successful"
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="VCH ID: 21"
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="VCH Admin Portal:"
time="2021-05-07T13:34:13+01:00" level=info msg="https://192.168.1.152:2378"
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="VCH Default Bridge Network Range: 172.16.0.0/12"
time="2021-05-07T13:34:13+01:00" level=info msg="VCH Default Bridge Network Width: 16"
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="Published ports can be reached at:"
time="2021-05-07T13:34:13+01:00" level=info msg=192.168.1.152
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="Management traffic will use:"
time="2021-05-07T13:34:13+01:00" level=info msg=192.168.1.152
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="Docker environment variables:"
time="2021-05-07T13:34:13+01:00" level=info msg="DOCKER_HOST=192.168.1.152:2375 COMPOSE_TLS_VERSION=TLSv1_2"
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="Environment saved in virtual-container-host/virtual-container-host.env"
time="2021-05-07T13:34:13+01:00" level=info
time="2021-05-07T13:34:13+01:00" level=info msg="Connect to docker:"
time="2021-05-07T13:34:13+01:00" level=info msg="docker -H 192.168.1.152:2375 info"
time="2021-05-07T13:34:13+01:00" level=info msg="Installer completed successfully"
PS X:\F. Software\VMWare VIC\VIC1.5.7\vic>
```


![](/attachments/Pasted%20image%2020210507135247.png)

Once created I want to check the local VCH and gain access to do that I am going enable SSH for a brief period:

```
.\vic-machine-windows.exe debug --target 192.168.1.99 --user root --password Imagin3! --id 21 --thumbprint 41:28:C4:9A:D7:52:C9:0D:5F:19:8F:98:B1:7A:4A:A4:08:B6:08:8B --enable-ssh --rootpw Password3
```

![](/attachments/Pasted%20image%2020210507135317.png)

Next we need to create the docker images so we will use a docker client. It is just a normal install of Docker but we are talking to the VCH via the Docker API. 

### Docker Client - CentOS7
To set up Docker on Centos 7 64-bit, run the following commands. The procedure first removes any previous instances of Docker and reinstalls it from scratch. Start off each command with _sudo_ if you’re logged in with a non-admin user account.

> yum remove docker docker-common container-selinux docker-selinux docker-engine

> yun install yum-utils

> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

> yum makecache fast

> yum install docker-ce


![](/attachments/Pasted%20image%2020210430145333.png)
####  Configure the Docker client environment
Next, we need to set the correct Docker client API version to match that used by the vSphere Integrated Containers Engine. To do this, just run the following:
```bash 
export DOCKER_API_VERSION=1.23
```

Optionally, set the _DOCKER\_HOST_ var as follows. If omitted, you must include -H <Docker IP:Port> to every Docker command run.

```bash
export DOCKER_HOST=192.168.1.152:2375
docker -H 192.168.1.152:2375 info
```
![](/attachments/Pasted%20image%2020210430170753.png)


### Create first Docker Container
Create your first virtual integrated container (VIC)

As an example, I’ve deployed a container running the _nginx_ webserver. Note, that the _run_ parameter will automatically connect to a Docker Hub (repository) from where it pulls down the corresponding Docker image which in turn is used to create the container. The _\-p_ argument, tells the Docker endpoint to publish port 80 for the container just created which I’ve called nginx1. The _nginx_ bit at the end of the command is in fact the search argument submitted to the Docker Hub.

```bash
docker -H 192.168.1.152:2375 run -d -p 80:80 --name nginx1 nginx
```	
	
![](/attachments/Pasted%20image%2020210430174115.png)
	
![](/attachments/Pasted%20image%2020210430174203.png)

and here we can see the new container running the ESXi Host environment
![](/attachments/Pasted%20image%2020210430174321.png)

Verify access to the service published by the container

I should now be able to access the landing page on the nginx web server running on the container by browsing to the VCH’s IP address on port 80. Success!
	
![](/attachments/Pasted%20image%2020210430174434.png)
	
Check that containers are running? 
```bash
docker -H 192.168.1.152:2375 ps	-a
```
![](/attachments/Pasted%20image%2020210430174645.png)
	
### Items to think about???
Does VIC, VCH work with Docker Swarm? 
	
Commands to remember 
- INPSECT
> .\vic-machine-windows.exe inspect --target 192.168.1.99 -user root -password Imagin3!  -thumbprint 41:28:C4:9A:D7:52:C9:0D:5F:19:8F:98:B1:7A:4A:A4:08:B6:08:8B
- SET SSH 
> .\vic-machine-windows.exe debug --target 192.168.1.99 --user root --password Imagin3! --id 23 --thumbprint 41:28:C4:9A:D7:52:C9:0D:5F:19:8F:98:B1:7A:4A:A4:08:B6:08:8B --enable-ssh --rootpw Imagin3!

## Compose installed on VHC
![](/attachments/Pasted%20image%2020210511134729.png)	

	
```sh 
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```
sudo chmod +x /usr/local/bin/docker-compose
```

> **Note**: If the command `docker-compose` fails after installation, check your path. You can also create a symbolic link to `/usr/bin` or any other directory in your path.

For example:

```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

	## Tools 
	https://codebeautify.org/yaml-validator/cbccd63a
	
	