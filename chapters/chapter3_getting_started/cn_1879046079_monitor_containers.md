- cn_1879046079_monitor_containers
	- Created on the: 2021-11-11 10:11
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
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / requirements ⚓ before you start you need to know.
---
# Whats going inside a running container?
commands that we need to learn:
- `docker container top`  - process list in 1 cotainer
- `docker container inspect` - details on 1 container config
- `docker container stats` - shows all docker container stats in realtime

🤸 Build 2 new containers  - nginx and mysql as we have done before.
```bash
docker run --name mysql-test -d -p 3306:3306 -e MYSQL_RANDOM_ROOT_PASSWORD=yes  mysql/mysql-server   
docker run --name nginx-test -d -p 80:80 nginx  
```
Now run through the above commands and review. 
## top
List the process of the containers
```bash
❯ docker container top mysql-test
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
27                  19340               19314               0                   10:14               ?                   00:00:00            mysqld --user=mysql
```
## inspect
this command will print out a `json` array of the container metadata. 
```bash
➜ docker container inspect nginx-test
[
    {
        "Id": "fec3b5478c0750d152a0ad2b40faf5c27be3d67873e5710fcd13737919fba040",
        "Created": "2021-11-11T10:14:54.04218242Z",
        "Path": "/docker-entrypoint.sh",
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 19686,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-11-11T10:14:55.243505129Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:f1325989da199841ef249c72bbbbc5011256fb908ca5b5c144d3a64b51257046",
        "ResolvConfPath": "/var/lib/docker/containers/fec3b5478c0750d152a0ad2b40faf5c27be3d67873e5710fcd13737919fba040/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/fec3b5478c0750d152a0ad2b40faf5c27be3d67873e5710fcd13737919fba040/hostname",
        "HostsPath": "/var/lib/docker/containers/fec3b5478c0750d152a0ad2b40faf5c27be3d67873e5710fcd13737919fba040/hosts",
        "LogPath": "/var/lib/docker/containers/fec3b5478c0750d152a0ad2b40faf5c27be3d67873e5710fcd13737919fba040/fec3b5478c0750d152a0ad2b40faf5c27be3d67873e5710fcd13737919fba040-json.log",
        "Name": "/nginx-test",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {
                "80/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "80"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/9c0d74fe3e54b233e9f0b47f47f51b455f407b524a08489b5bda5f76d70a4e71-init/diff:/var/lib/docker/overlay2/3e9aeb638ad470651d85a42a48ae2dbed6c44afd66020109718352dc95654e0d/diff:/var/lib/docker/overlay2/63be4f8a75d0ac71ab71afcca3d70deeec69abe2593ed707201ad93d803bb222/diff:/var/lib/docker/overlay2/ee00d5a36b368013c00355cdbc6b1208f381670b111ad0b58b1ac6521e3797f8/diff:/var/lib/docker/overlay2/d5869765dc1660b6599279fa94184234ca9246b2433df2445b4e03f292caa97b/diff:/var/lib/docker/overlay2/b99e0d8c8101738b0fb808753b0d3d3f638f000a3ff663336f3cbd374f4ccf59/diff:/var/lib/docker/overlay2/e6cc2cb070bad1a973fa0a92e164ba213d6b04f9763aa3ca8cf47a76f8613247/diff",
                "MergedDir": "/var/lib/docker/overlay2/9c0d74fe3e54b233e9f0b47f47f51b455f407b524a08489b5bda5f76d70a4e71/merged",
                "UpperDir": "/var/lib/docker/overlay2/9c0d74fe3e54b233e9f0b47f47f51b455f407b524a08489b5bda5f76d70a4e71/diff",
                "WorkDir": "/var/lib/docker/overlay2/9c0d74fe3e54b233e9f0b47f47f51b455f407b524a08489b5bda5f76d70a4e71/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "fec3b5478c07",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.21.4",
                "NJS_VERSION=0.7.0",
                "PKG_RELEASE=1~bullseye"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "nginx",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers \u003cdocker-maint@nginx.com\u003e"
            },
            "StopSignal": "SIGQUIT"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "f368065ed8b4ef57e4f65b1d98bbf0a2304ae10d0fdece21082b5048d7e5540d",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "80/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "80"
                    }
                ]
            },
            "SandboxKey": "/var/run/docker/netns/f368065ed8b4",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "82090cbcf90ef623b1d8f98358e1b1dfca4428c55dfbc8cfab634ae6f31626ec",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.3",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:03",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "a6f482f1b12362f2e782e85b561f1d172506a0204319a11d3b8ce3a40a57ac2c",
                    "EndpointID": "82090cbcf90ef623b1d8f98358e1b1dfca4428c55dfbc8cfab634ae6f31626ec",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03",
                    "DriverOpts": null
                }
            }
        }
    }
]
```
This is not a live view of the container, but how the container is built. 

## stats
for a live view we need to use the `docker container stats` command. 
run the help on this command to see the options. 
```bash
❯ docker container stats --help

Usage:  docker container stats [OPTIONS] [CONTAINER...]

Display a live stream of container(s) resource usage statistics

Options:
  -a, --all             Show all containers (default shows just running)
      --format string   Pretty-print images using a Go template
      --no-stream       Disable streaming stats and only pull the first result
      --no-trunc        Do not truncate output
```
sea all the containers running stats - live - run `docker container stats`
```bash
CONTAINER ID   NAME         CPU %     MEM USAGE / LIMIT     MEM %     NET I/O       BLOCK I/O         PIDS
fec3b5478c07   nginx-test   0.00%     4.961MiB / 1.942GiB   0.25%     936B / 0B     1.11MB / 8.19kB   5
86ed817de21d   mysql-test   0.45%     370.3MiB / 1.942GiB   18.62%    1.16kB / 0B   2.16MB / 244MB    38
```
`ctrl=c` for sig term

As we build our learning this command is going to become less use, as you will use bigger more complex monitoring tools. But this is useful for local small deployment. 

