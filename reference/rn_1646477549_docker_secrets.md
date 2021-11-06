- rn_1646477549_docker_secrets
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-09-06 17:04
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://docs.docker.com/engine/swarm/secrets/
		- https://docs.docker.com/compose/compose-file/compose-file-v3/#secrets
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# Manage sensitive data with Docker secrets

## About secrets[](https://docs.docker.com/engine/swarm/secrets/#about-secrets)

In terms of Docker Swarm services, a _secret_ is a blob of data, such as a password, SSH private key, SSL certificate, or another piece of data that should not be transmitted over a network or stored unencrypted in a Dockerfile or in your application’s source code. You can use Docker _secrets_ to centrally manage this data and securely transmit it to only those containers that need access to it. Secrets are encrypted during transit and at rest in a Docker swarm. A given secret is only accessible to those services which have been granted explicit access to it, and only while those service tasks are running.

You can use secrets to manage any sensitive data which a container needs at runtime but you don’t want to store in the image or in source control, such as:

-   Usernames and passwords
-   TLS certificates and keys
-   SSH keys
-   Other important data such as the name of a database or internal server
-   Generic strings or binary content (up to 500 kb in size)

> Knowledge 🧠 **Note**: Docker secrets are only available to swarm services, not to standalone containers. To use this feature, consider adapting your container to run as a service. Stateful containers can typically run with a scale of 1 without changing the container code.

Another use case for using secrets is to provide a layer of abstraction between the container and a set of credentials. Consider a scenario where you have separate development, test, and production environments for your application. Each of these environments can have different credentials, stored in the development, test, and production swarms with the same secret name. Your containers only need to know the name of the secret to function in all three environments.

You can also use secrets to manage non-sensitive data, such as configuration files. However, Docker supports the use of [configs](https://docs.docker.com/engine/swarm/configs/) for storing non-sensitive data. Configs are mounted into the container’s filesystem directly, without the use of a RAM disk.

## How Docker manages secrets[](https://docs.docker.com/engine/swarm/secrets/#how-docker-manages-secrets)

When you add a secret to the swarm, Docker sends the secret to the swarm manager over a mutual TLS connection. The secret is stored in the Raft log, which is encrypted. The entire Raft log is replicated across the other managers, ensuring the same high availability guarantees for secrets as for the rest of the swarm management data.

When you grant a newly-created or running service access to a secret, the decrypted secret is mounted into the container in an in-memory filesystem. The location of the mount point within the container defaults to `/run/secrets/<secret_name>` in Linux containers, or `C:\ProgramData\Docker\secrets` in Windows containers. You can also specify a custom location.

You can update a service to grant it access to additional secrets or revoke its access to a given secret at any time.

A node only has access to (encrypted) secrets if the node is a swarm manager or if it is running service tasks which have been granted access to the secret. When a container task stops running, the decrypted secrets shared to it are unmounted from the in-memory filesystem for that container and flushed from the node’s memory.

If a node loses connectivity to the swarm while it is running a task container with access to a secret, the task container still has access to its secrets, but cannot receive updates until the node reconnects to the swarm.

You can add or inspect an individual secret at any time, or list all secrets. You cannot remove a secret that a running service is using. See [Rotate a secret](https://docs.docker.com/engine/swarm/secrets/#example-rotate-a-secret) for a way to remove a secret without disrupting running services.

To update or roll back secrets more easily, consider adding a version number or date to the secret name. This is made easier by the ability to control the mount point of the secret within a given container.

## Read more about `docker secret` commands[](https://docs.docker.com/engine/swarm/secrets/#read-more-about-docker-secret-commands)

Use these links to read about specific commands, or continue to the [example about using secrets with a service](https://docs.docker.com/engine/swarm/secrets/#example-use-secrets-with-a-service).

-   [`docker secret create`](https://docs.docker.com/engine/reference/commandline/secret_create/)
-   [`docker secret inspect`](https://docs.docker.com/engine/reference/commandline/secret_inspect/)
-   [`docker secret ls`](https://docs.docker.com/engine/reference/commandline/secret_ls/)
-   [`docker secret rm`](https://docs.docker.com/engine/reference/commandline/secret_rm/)
-   [`--secret`](https://docs.docker.com/engine/reference/commandline/service_create/#create-a-service-with-secrets) flag for `docker service create`
-   [`--secret-add` and `--secret-rm`](https://docs.docker.com/engine/reference/commandline/service_update/#adding-and-removing-secrets) flags for `docker service update`

1.  Add a secret to Docker. The `docker secret create` command reads standard input because the last argument, which represents the file to read the secret from, is set to `-`.
    

```
 printf "This is a secret" | docker secret create my_secret_data -
```

Create a secret from file
```
docker secret create my_secret ./secret

```

### secrets[](https://docs.docker.com/compose/compose-file/compose-file-v3/#secrets)

Grant access to secrets on a per-service basis using the per-service `secrets` configuration. Two different syntax variants are supported.

> Note when using docker stack deploy
> 
> The secret must already exist or be [defined in the top-level `secrets` configuration](https://docs.docker.com/compose/compose-file/compose-file-v3/#secrets-configuration-reference) of the compose file, or stack deployment fails.

#### Short syntax

The short syntax variant only specifies the secret name. This grants the container access to the secret and mounts it at `/run/secrets/<secret_name>` within the container. The source name and destination mountpoint are both set to the secret name.

The following example uses the short syntax to grant the `redis` service access to the `my_secret` and `my_other_secret` secrets. The value of `my_secret` is set to the contents of the file `./my_secret.txt`, and `my_other_secret` is defined as an external resource, which means that it has already been defined in Docker, either by running the `docker secret create` command or by another stack deployment. If the external secret does not exist, the stack deployment fails with a `secret not found` error.

```yml
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - my_secret
      - my_other_secret
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```

#### Long syntax

The long syntax provides more granularity in how the secret is created within the service’s task containers.

-   `source`: The identifier of the secret as it is defined in this configuration.
-   `target`: The name of the file to be mounted in `/run/secrets/` in the service’s task containers. Defaults to `source` if not specified.
-   `uid` and `gid`: The numeric UID or GID that owns the file within `/run/secrets/` in the service’s task containers. Both default to `0` if not specified.
-   `mode`: The permissions for the file to be mounted in `/run/secrets/` in the service’s task containers, in octal notation. For instance, `0444` represents world-readable. The default in Docker 1.13.1 is `0000`, but is be `0444` in newer versions. Secrets cannot be writable because they are mounted in a temporary filesystem, so if you set the writable bit, it is ignored. The executable bit can be set. If you aren’t familiar with UNIX file permission modes, you may find this [permissions calculator](http://permissions-calculator.org/) useful.

The following example sets name of the `my_secret` to `redis_secret` within the container, sets the mode to `0440` (group-readable) and sets the user and group to `103`. The `redis` service does not have access to the `my_other_secret` secret.

```yml
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - source: my_secret
        target: redis_secret
        uid: '103'
        gid: '103'
        mode: 0440
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```

You can grant a service access to multiple secrets and you can mix long and short syntax. Defining a secret does not imply granting a service access to it.

 ![](/attachments/Pasted%20image%2020211029122848.png)

![](/attachments/Pasted%20image%2020211029123213.png)

## Using secrets in a swarm stack
Exercise pack - use 