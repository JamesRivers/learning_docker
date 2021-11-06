- rn_1645641497_docker_config_commands
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-09-06 12:44
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://docs.docker.com/engine/swarm/configs/
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Store configuration data using Docker Configs

## About configs[](https://docs.docker.com/engine/swarm/configs/#about-configs)

Docker swarm service configs allow you to store non-sensitive information, such as configuration files, outside a service’s image or running containers. This allows you to keep your images as generic as possible, without the need to bind-mount configuration files into the containers or use environment variables.

Configs operate in a similar way to [secrets](https://docs.docker.com/engine/swarm/secrets/), except that they are not encrypted at rest and are mounted directly into the container’s filesystem without the use of RAM disks. Configs can be added or removed from a service at any time, and services can share a config. You can even use configs in conjunction with environment variables or labels, for maximum flexibility. Config values can be generic strings or binary content (up to 500 kb in size).

> **Note**: Docker configs are only available to swarm services, not to standalone containers. To use this feature, consider adapting your container to run as a service with a scale of 1.

## How Docker manages configs[](https://docs.docker.com/engine/swarm/configs/#how-docker-manages-configs)

When you add a config to the swarm, Docker sends the config to the swarm manager over a mutual TLS connection. The config is stored in the Raft log, which is encrypted. The entire Raft log is replicated across the other managers, ensuring the same high availability guarantees for configs as for the rest of the swarm management data.

When you grant a newly-created or running service access to a config, the config is mounted as a file in the container. The location of the mount point within the container defaults to `/<config-name>` in Linux containers. In Windows containers, configs are all mounted into `C:\ProgramData\Docker\configs` and symbolic links are created to the desired location, which defaults to `C:\<config-name>`.

You can set the ownership (`uid` and `gid`) for the config, using either the numerical ID or the name of the user or group. You can also specify the file permissions (`mode`). These settings are ignored for Windows containers.

-   If not set, the config is owned by the user running the container command (often `root`) and that user’s default group (also often `root`).
-   If not set, the config has world-readable permissions (mode `0444`), unless a `umask` is set within the container, in which case the mode is impacted by that `umask` value.

You can update a service to grant it access to additional configs or revoke its access to a given config at any time.

A node only has access to configs if the node is a swarm manager or if it is running service tasks which have been granted access to the config. When a container task stops running, the configs shared to it are unmounted from the in-memory filesystem for that container and flushed from the node’s memory.

**If a node loses connectivity to the swarm while it is running a task container with access to a config, the task container still has access to its configs, but cannot receive updates until the node reconnects to the swarm.**

You can add or inspect an individual config at any time, or list all configs. You cannot remove a config that a running service is using. See [Rotate a config](https://docs.docker.com/engine/swarm/configs/#example-rotate-a-config) for a way to remove a config without disrupting running services.

To update or roll back configs more easily, consider adding a version number or date to the config name. This is made easier by the ability to control the mount point of the config within a given container.

To update a stack, make changes to your Compose file, then re-run `docker stack deploy -c <new-compose-file> <stack-name>`. If you use a new config in that file, your services start using them. Keep in mind that configurations are immutable, so you can’t change the file for an existing service. Instead, you create a new config to use a different file

You can run `docker stack rm` to stop the app and take down the stack. This removes any config that was created by `docker stack deploy` with the same stack name. This removes _all_ configs, including those not referenced by services and those remaining after a `docker service update --config-rm`.

## Read more about `docker config` commands[](https://docs.docker.com/engine/swarm/configs/#read-more-about-docker-config-commands)

Use these links to read about specific commands, or continue to the [example about using configs with a service](https://docs.docker.com/engine/swarm/configs/#advanced-example-use-configs-with-a-nginx-service).

-   [`docker config create`](https://docs.docker.com/engine/reference/commandline/config_create/)
-   [`docker config inspect`](https://docs.docker.com/engine/reference/commandline/config_inspect/)
-   [`docker config ls`](https://docs.docker.com/engine/reference/commandline/config_ls/)
-   [`docker config rm`](https://docs.docker.com/engine/reference/commandline/config_rm/)

## Examples[](https://docs.docker.com/engine/swarm/configs/#examples)

This section includes graduated examples which illustrate how to use Docker configs.

> **Note**: These examples use a single-Engine swarm and unscaled services for simplicity. The examples use Linux containers, but Windows containers also support configs.

### Defining and using configs in compose files[](https://docs.docker.com/engine/swarm/configs/#defining-and-using-configs-in-compose-files)

The `docker stack` command supports defining configs in a Compose file. However, the `configs` key is not supported for `docker compose`. See [the Compose file reference](https://docs.docker.com/compose/compose-file/compose-file-v3/#configs) for details.

### Simple example: Get started with configs[](https://docs.docker.com/engine/swarm/configs/#simple-example-get-started-with-configs)

This simple example shows how configs work in just a few commands. For a real-world example, continue to [Advanced example: Use configs with a Nginx service](https://docs.docker.com/engine/swarm/configs/#advanced-example-use-configs-with-a-nginx-service).


### configs[](https://docs.docker.com/compose/compose-file/compose-file-v3/#configs)

Grant access to configs on a per-service basis using the per-service `configs` configuration. Two different syntax variants are supported.

> **Note**: The config must already exist or be [defined in the top-level `configs` configuration](https://docs.docker.com/compose/compose-file/compose-file-v3/#configs-configuration-reference) of this stack file, or stack deployment fails.

For more information on configs, see [configs](https://docs.docker.com/engine/swarm/configs/).

#### Short syntax

The short syntax variant only specifies the config name. This grants the container access to the config and mounts it at `/<config_name>` within the container. The source name and destination mountpoint are both set to the config name.

The following example uses the short syntax to grant the `redis` service access to the `my_config` and `my_other_config` configs. The value of `my_config` is set to the contents of the file `./my_config.txt`, and `my_other_config` is defined as an external resource, which means that it has already been defined in Docker, either by running the `docker config create` command or by another stack deployment. If the external config does not exist, the stack deployment fails with a `config not found` error.

> Added in [version 3.3](https://docs.docker.com/compose/compose-file/compose-versioning/#version-33) file format.
> 
> `config` definitions are only supported in version 3.3 and higher of the compose file format.

```
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    configs:
      - my_config
      - my_other_config
configs:
  my_config:
    file: ./my_config.txt
  my_other_config:
    external: true
```

#### Long syntax

The long syntax provides more granularity in how the config is created within the service’s task containers.

-   `source`: The identifier of the config as it is defined in this configuration.
-   `target`: The path and name of the file to be mounted in the service’s task containers. Defaults to `/<source>` if not specified.
-   `uid` and `gid`: The numeric UID or GID that owns the mounted config file within in the service’s task containers. Both default to `0` on Linux if not specified. Not supported on Windows.
-   `mode`: The permissions for the file that is mounted within the service’s task containers, in octal notation. For instance, `0444` represents world-readable. The default is `0444`. Configs cannot be writable because they are mounted in a temporary filesystem, so if you set the writable bit, it is ignored. The executable bit can be set. If you aren’t familiar with UNIX file permission modes, you may find this [permissions calculator](http://permissions-calculator.org/) useful.

The following example sets the name of `my_config` to `redis_config` within the container, sets the mode to `0440` (group-readable) and sets the user and group to `103`. The `redis` service does not have access to the `my_other_config` config.

```
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    configs:
      - source: my_config
        target: /redis_config
        uid: '103'
        gid: '103'
        mode: 0440
configs:
  my_config:
    file: ./my_config.txt
  my_other_config:
    external: true
```

You can grant a service access to multiple configs and you can mix long and short syntax. Defining a config does not imply granting a service access to it.


### In the Versio world 
I would use configs for files like the `appsettings.overrides.json` file. Rather than bind mount that file - why do I not just store this file in a docker config set up - so all hermes instances can access this file in the swarm. 

```yaml
version: '3.8'

services:
  hermes:
    image: deploy.imaginecommunications.com:443/versio-docker-dev/imagine-hermes-core:2.2.0.7-alpine
    ports:
      - target: 9872
        published: 9872
        protocol: tcp
        mode: host
    networks:
      - wyc-dev-network
    configs:
    - source: hermes_pg_config
      target: /hermes/.imaginecommunications/imagine-hermes-core/appsettings.overrides.json
    environment:
      - ProgramData=/hermes/programdata
      - HOME=/hermes
      - CUSTOM_HERMES_HOST={{.Node.Hostname}}
    deploy:
      mode: global
networks:
    wyc-dev-network:
        external: true
configs:
    hermes_pg_config:
        external: true
```

In order for this to work I need to create a config using the `docker config` command. Then list the `configs:` tag in the compose file.  Plus declare the target. And also list the config as an external item. 
```yml 
configs:
    hermes_pg_config:
        external: true
```

### How to update the config file?
If I need to adjust the json file I will create a new docker config file

old = 
![](/attachments/Pasted%20image%2020210906142358.png)

new =
![](/attachments/Pasted%20image%2020210906142431.png)

then perform the following

```yml
docker service update   --config-rm hermes_pg_config   --config-add source=hermes_pg_config_new,target=/hermes/.imaginecommunications/imagine-hermes-core/appsettings.overrides.json hermes_hermes 
```

Each container in the service will be updated 1 at a time.
![](/attachments/Pasted%20image%2020210906142812.png)



