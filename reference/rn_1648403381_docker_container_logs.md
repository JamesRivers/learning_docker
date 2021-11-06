- rn_1648403381_docker_container_logs
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-09-07 11:41
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://docs.docker.com/engine/reference/commandline/logs/
	- Author Notes: 
	- Tags: [readme](readme.md)
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# docker logs
Fetch the logs for a container. 

How?

## CLI

```
docker logs [OPTIONS] CONTAINER
```

More information about selecting and configuring logging drivers, refer to [Configure logging drivers](https://docs.docker.com/config/containers/logging/configure/).

The `docker logs --follow` command will continue streaming the new output from the container’s `STDOUT` and `STDERR`.

Passing a negative number or a non-integer to `--tail` is invalid and the value is set to `all` in that case.

The `docker logs --timestamps` command will add an [RFC3339Nano timestamp](https://golang.org/pkg/time/#pkg-constants) , for example `2014-09-16T06:17:46.000000000Z`, to each log entry. To ensure that the timestamps are aligned the nano-second part of the timestamp will be padded with zero when necessary.

The `docker logs --details` command will add on extra attributes, such as environment variables and labels, provided to `--log-opt` when creating the container.

The `--since` option shows only the container logs generated after a given date. You can specify the date as an RFC 3339 date, a UNIX timestamp, or a Go duration string (e.g. `1m30s`, `3h`). Besides RFC3339 date format you may also use RFC3339Nano, `2006-01-02T15:04:05`, `2006-01-02T15:04:05.999999999`, `2006-01-02Z07:00`, and `2006-01-02`. The local timezone on the client will be used if you do not provide either a `Z` or a `+-00:00` timezone offset at the end of the timestamp. When providing Unix timestamps enter seconds[.nanoseconds], where seconds is the number of seconds that have elapsed since January 1, 1970 (midnight UTC/GMT), not counting leap seconds (aka Unix epoch or Unix time), and the optional .nanoseconds field is a fraction of a second no more than nine digits long. You can combine the `--since` option with either or both of the `--follow` or `--tail` options.

For example uses of this command, refer to the [examples section](https://docs.docker.com/engine/reference/commandline/logs/#examples) below.

![](/attachments/Pasted%20image%2020210907114350.png)

### Retrieve logs until a specific point in time[](https://docs.docker.com/engine/reference/commandline/logs/#retrieve-logs-until-a-specific-point-in-time)

In order to retrieve logs before a specific point in time, run:
![](/attachments/Pasted%20image%2020210907114420.png)

## Portainer 
Open the `portainer` web app find the container and click on the logs icon to review the logs. 

![](/attachments/Pasted%20image%2020210907114526.png)

![](/attachments/Pasted%20image%2020210907114543.png)