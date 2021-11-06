- rn_1604971395_docker_compose_environment_variables
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-08-24 11:56
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- [Docker Compose Environment Variables](https://docs.docker.com/compose/environment-variables/)
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
## Substitute environment variables in Compose files[](https://docs.docker.com/compose/environment-variables/#substitute-environment-variables-in-compose-files)

It’s possible to use environment variables in your shell to populate values inside a Compose file:
```
web:
  image: "webapp:${TAG}"
```
If you have multiple environment variables, you can substitute them by adding them to a default environment variable file named `.env` or by providing a path to your environment variables file using the `--env-file` command line option.

### The “.env” file[](https://docs.docker.com/compose/environment-variables/#the-env-file)

You can set default values for any environment variables referenced in the Compose file, or used to configure Compose, in an [environment file](https://docs.docker.com/compose/env-file/) named `.env`. The `.env` file path is as follows:

-   Starting with `+v1.28`, `.env` file is placed at the base of the project directory
-   Project directory can be explicitly defined with the `--file` option or `COMPOSE_FILE` environment variable. Otherwise, it is the current working directory where the `docker compose` command is executed (`+1.28`).
-   For previous versions, it might have trouble resolving `.env` file with `--file` or `COMPOSE_FILE`. To work around it, it is recommended to use `--project-directory`, which overrides the path for the `.env` file. This inconsistency is addressed in `+v1.28` by limiting the filepath to the project directory.

![](/attachments/Pasted%20image%2020210824120012.png)

When you run `docker-compose up`, the `web` service defined above uses the image `webapp:v1.5`. You can verify this with the [config command](https://docs.docker.com/compose/reference/config/), which prints your resolved application config to the terminal:
![](/attachments/Pasted%20image%2020210824120059.png)

Values in the shell take precedence over those specified in the `.env` file.

If you set `TAG` to a different value in your shell, the substitution in `image` uses that instead:

![](/attachments/Pasted%20image%2020210824120129.png)

You can override the environment file path using a command line argument `--env-file`.

### Using the “--env-file” option[](https://docs.docker.com/compose/environment-variables/#using-the---env-file--option)

By passing the file as an argument, you can store it anywhere and name it appropriately, for example, `.env.ci`, `.env.dev`, `.env.prod`. Passing the file path is done using the `--env-file` option:

```
 docker-compose --env-file ./config/.env.dev up
```

This file path is relative to the current working directory where the Docker Compose command is executed.
![](/attachments/Pasted%20image%2020210824120244.png)
The `.env` file is loaded by default:
![](/attachments/Pasted%20image%2020210824120320.png)
Passing the `--env-file ` argument overrides the default file path:
![](/attachments/Pasted%20image%2020210824120332.png)

When an invalid file path is being passed as `--env-file` argument, Compose returns an error:
![](/attachments/Pasted%20image%2020210824120352.png)
