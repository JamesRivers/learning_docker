- cn_1879064246_docker_data_volumes
	- Created on the: 2021-11-22 16:50
	- Review date:
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
	- Sources: 
		- https://docs.docker.com/storage/volumes/
		- 
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# Docker Data Volumes 
- `VOLUME` command in Dockerfile
- Also override with docker run -v /path/in/container
- Bypasses Union File System and stores in alt location on host
- Includes it's own management commands under docker volume
- Connect to none, one, or multiple containers at once
- Not subject to commit, save, or export commands
- By default they only have a unique ID, but you can assign name
- Then it's a "named volume"

## Docker File - Volume 
Take a gander at a `mysql` docker file to review the `volume` option 
```bash
#
# NOTE: THIS DOCKERFILE IS GENERATED VIA "apply-templates.sh"
#
# PLEASE DO NOT EDIT IT DIRECTLY.
#

FROM debian:buster-slim

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r mysql && useradd -r -g mysql mysql

RUN apt-get update && apt-get install -y --no-install-recommends gnupg dirmngr && rm -rf /var/lib/apt/lists/*

# add gosu for easy step-down from root
# https://github.com/tianon/gosu/releases
ENV GOSU_VERSION 1.12
RUN set -eux; \
	savedAptMark="$(apt-mark showmanual)"; \
	apt-get update; \
	apt-get install -y --no-install-recommends ca-certificates wget; \
	rm -rf /var/lib/apt/lists/*; \
	dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	gpgconf --kill all; \
	rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc; \
	apt-mark auto '.*' > /dev/null; \
	[ -z "$savedAptMark" ] || apt-mark manual $savedAptMark > /dev/null; \
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	chmod +x /usr/local/bin/gosu; \
	gosu --version; \
	gosu nobody true

RUN mkdir /docker-entrypoint-initdb.d

RUN apt-get update && apt-get install -y --no-install-recommends \
# for MYSQL_RANDOM_ROOT_PASSWORD
		pwgen \
# for mysql_ssl_rsa_setup
		openssl \
# FATAL ERROR: please install the following Perl modules before executing /usr/local/mysql/scripts/mysql_install_db:
# File::Basename
# File::Copy
# Sys::Hostname
# Data::Dumper
		perl \
# install "xz-utils" for .sql.xz docker-entrypoint-initdb.d files
		xz-utils \
	&& rm -rf /var/lib/apt/lists/*

RUN set -ex; \
# gpg: key 5072E1F5: public key "MySQL Release Engineering <mysql-build@oss.oracle.com>" imported
	key='A4A9406876FCBD3C456770C88C718D3B5072E1F5'; \
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "$key"; \
	gpg --batch --export "$key" > /etc/apt/trusted.gpg.d/mysql.gpg; \
	gpgconf --kill all; \
	rm -rf "$GNUPGHOME"; \
	apt-key list > /dev/null

ENV MYSQL_MAJOR 8.0
ENV MYSQL_VERSION 8.0.27-1debian10

RUN echo 'deb http://repo.mysql.com/apt/debian/ buster mysql-8.0' > /etc/apt/sources.list.d/mysql.list

# the "/var/lib/mysql" stuff here is because the mysql-server postinst doesn't have an explicit way to disable the mysql_install_db codepath besides having a database already "configured" (ie, stuff in /var/lib/mysql/mysql)
# also, we set debconf keys to make APT a little quieter
RUN { \
		echo mysql-community-server mysql-community-server/data-dir select ''; \
		echo mysql-community-server mysql-community-server/root-pass password ''; \
		echo mysql-community-server mysql-community-server/re-root-pass password ''; \
		echo mysql-community-server mysql-community-server/remove-test-db select false; \
	} | debconf-set-selections \
	&& apt-get update \
	&& apt-get install -y \
		mysql-community-client="${MYSQL_VERSION}" \
		mysql-community-server-core="${MYSQL_VERSION}" \
	&& rm -rf /var/lib/apt/lists/* \
	&& rm -rf /var/lib/mysql && mkdir -p /var/lib/mysql /var/run/mysqld \
	&& chown -R mysql:mysql /var/lib/mysql /var/run/mysqld \
# ensure that /var/run/mysqld (used for socket and lock files) is writable regardless of the UID our mysqld instance ends up having at runtime
	&& chmod 1777 /var/run/mysqld /var/lib/mysql

VOLUME /var/lib/mysql

# Config files
COPY config/ /etc/mysql/
COPY docker-entrypoint.sh /usr/local/bin/
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3306 33060
CMD ["mysqld"]
```

`Volumes` out live containers... `Volumes` need to be reviewed and removed as requried. 

Pull the mysql image and review the container once you have it running:
```bash
docker container run --rm --name mysql  --platform linux/amd64 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql 
```

Then run a `docker container inspect` and review the volume and mount points

```bash
"Mounts": [
            {
                "Type": "volume",
                "Name": "3871dcd925f8d85e79378f9f59b135fb822845501d4f9bf37f09b99c3b1a1604",
                "Source": "/var/lib/docker/volumes/3871dcd925f8d85e79378f9f59b135fb822845501d4f9bf37f09b99c3b1a1604/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
```
```bash
       "Volumes": {
                "/var/lib/mysql": {}
            },
```

run a `docker volume ls`
```bash
❯ docker volume ls
DRIVER    VOLUME NAME
local     366a1666d59e72dde3b2c3230e02d472bb8b431875e4cdc77d4e119ebaf8df1d
local     3871dcd925f8d85e79378f9f59b135fb822845501d4f9bf37f09b99c3b1a1604
local     a822bf4495c4a8ff3fd587a722162ccbf57eee98f4ec45a0d2a49b35b4839b36
```
and `docker volume inspect`
```bash
➜ docker volume inspect 3871dcd925f8d85e79378f9f59b135fb822845501d4f9bf37f09b99c3b1a1604
[
    {
        "CreatedAt": "2021-11-22T17:01:44Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/3871dcd925f8d85e79378f9f59b135fb822845501d4f9bf37f09b99c3b1a1604/_data",
        "Name": "3871dcd925f8d85e79378f9f59b135fb822845501d4f9bf37f09b99c3b1a1604",
        "Options": null,
        "Scope": "local"
    }
]
```

not very user friendly .. we should look at creating named volumes... That will help - so run the command as follows:
```baah
➜ docker container run --rm --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true -v name-mysql-db:/var/lib/mysql mysql 
```

`-v` added a name option before declaring the volume path. 

```bash
❯ docker volume ls                                                                      
DRIVER    VOLUME NAME
local     366a1666d59e72dde3b2c3230e02d472bb8b431875e4cdc77d4e119ebaf8df1d
local     a822bf4495c4a8ff3fd587a722162ccbf57eee98f4ec45a0d2a49b35b4839b36
local     name-mysql-db
```

Easier? 


