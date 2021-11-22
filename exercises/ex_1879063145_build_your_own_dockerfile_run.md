- ex_1879063145_build_your_own_dockerfile_run
	- Created on the: 2021-11-22 14:31
	- Review date:
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
	- Sources: 
	- Author Notes: 
	- Tags: 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# Build your own image... 
Exercise 🤸 - Hands on time..
- Dockerfiles are part process workflow and part art
- Take existing Node.js app and Dockerize it
- Make Dockerfile. Build it. Test it. Push it. (rm it). Run it.
- Expect this to be iterative. Rarely do I get it right the first time.
- Details in dockerfile-assignment-1/Dockerfile
	- https://github.com/BretFisher/udemy-docker-mastery/tree/main/dockerfile-assignment-1
- Use the Alpine version of the official 'node' 17.x image
- Expected result is web site at http://localhost
- Tag and push to your Docker Hub account (free)
- Remove your image from local cache, run again from Hub

## Steps
- Pull the files as listed in the docker file assignment
- make a `docker file` 
```bash
FROM node:17-alpine
EXPOSE 3000
RUN apk add --update tini
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
RUN npm install && npm cache clean
COPY . .
CMD ["tini", "--", "node", "./bin/www"]
```

Build and review the errors 
```bash
❯ docker build  -t testnode .                       
[+] Building 10.8s (9/10)                                                                                                                                    
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 37B                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 35B                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/node:17-alpine                                                                                       0.8s
 => [1/6] FROM docker.io/library/node:17-alpine@sha256:993bdfb0da7ae8fa4dad7282f797e3e26e88f810d410e0b0409d132d1fb17af3                                 8.4s
 => => resolve docker.io/library/node:17-alpine@sha256:993bdfb0da7ae8fa4dad7282f797e3e26e88f810d410e0b0409d132d1fb17af3                                 0.0s
 => => sha256:993bdfb0da7ae8fa4dad7282f797e3e26e88f810d410e0b0409d132d1fb17af3 1.43kB / 1.43kB                                                          0.0s
 => => sha256:29040181349b40af885052634ab7619aa9fad6876f5d26cba96b81a5baaaf5ac 1.16kB / 1.16kB                                                          0.0s
 => => sha256:365120f4efcab8b7aea6a0120c3c1264ceca83efb3745e87545dd45a40dc4e1e 6.54kB / 6.54kB                                                          0.0s
 => => sha256:ec7054e4c162b689fa7b4eaa7f659cfcd72329b04e22e00e629f2033ba3a2ddf 45.55MB / 45.55MB                                                        6.7s
 => => sha256:a535c5b315cd159560384e3871b93a6f2ae6a8f7f82fe2b8e187751dc576e813 2.41MB / 2.41MB                                                          0.5s
 => => sha256:2acf8c1667f99f5758b0f8c6312e492b869135e8c88b1e7b96a71114bf16b8e3 449B / 449B                                                              0.3s
 => => extracting sha256:ec7054e4c162b689fa7b4eaa7f659cfcd72329b04e22e00e629f2033ba3a2ddf                                                               1.3s
 => => extracting sha256:a535c5b315cd159560384e3871b93a6f2ae6a8f7f82fe2b8e187751dc576e813                                                               0.1s
 => => extracting sha256:2acf8c1667f99f5758b0f8c6312e492b869135e8c88b1e7b96a71114bf16b8e3                                                               0.0s
 => [internal] load build context                                                                                                                       0.0s
 => => transferring context: 1.11kB                                                                                                                     0.0s
 => [2/6] RUN apk add --update tini                                                                                                                     0.8s
 => [3/6] RUN mkdir -p /usr/src/app                                                                                                                     0.2s
 => [4/6] WORKDIR /usr/src/app                                                                                                                          0.0s 
 => ERROR [5/6] RUN npm install && npm cache clean                                                                                                      0.5s 
------
 > [5/6] RUN npm install && npm cache clean:
#7 0.459 npm ERR! code ENOENT
#7 0.459 npm ERR! syscall open
#7 0.459 npm ERR! path /usr/src/app/package.json
#7 0.460 npm ERR! errno -2
#7 0.461 npm ERR! enoent ENOENT: no such file or directory, open '/usr/src/app/package.json'
#7 0.461 npm ERR! enoent This is related to npm not being able to find a file.
#7 0.461 npm ERR! enoent 
#7 0.465 
#7 0.465 npm ERR! A complete log of this run can be found in:
#7 0.465 npm ERR!     /root/.npm/_logs/2021-11-22T16_25_35_995Z-debug.log
------
executor failed running [/bin/sh -c npm install && npm cache clean]: exit code: 254
```

Right we missed the copy of the `package.json` edit the `Dockerfile` - try again 
```bash
FROM node:17-alpine
EXPOSE 3000
RUN apk add --update tini
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY package.json package.json
RUN npm install && npm cache clean
COPY . .
CMD ["tini", "--", "node", "./bin/www"]
```

Again review the errors and resolve.  This time we failed on Step 6/7 
```bash
 => [5/7] COPY package.json package.json                                                                                                                0.0s
 => ERROR [6/7] RUN npm install && npm cache clean                                                                                                      3.3s
------                                                                                                                                                       
 > [6/7] RUN npm install && npm cache clean:                                                                                                                 
#10 3.063                                                                                                                                                    
#10 3.063 added 69 packages, and audited 70 packages in 3s                                                                                                   
#10 3.067                                                                                                                                                    
#10 3.067 4 vulnerabilities (2 moderate, 2 critical)                                                                                                         
#10 3.067 
#10 3.067 To address all issues, run:
#10 3.067   npm audit fix --force
#10 3.067 
#10 3.067 Run `npm audit` for details.
#10 3.068 npm notice 
#10 3.068 npm notice New patch version of npm available! 8.1.2 -> 8.1.4
#10 3.068 npm notice Changelog: <https://github.com/npm/cli/releases/tag/v8.1.4>
#10 3.068 npm notice Run `npm install -g npm@8.1.4` to update!
#10 3.068 npm notice 
#10 3.253 npm ERR! As of npm@5, the npm cache self-heals from corruption issues
#10 3.253 npm ERR!   by treating integrity mismatches as cache misses.  As a result,
#10 3.253 npm ERR!   data extracted from the cache is guaranteed to be valid.  If you
#10 3.253 npm ERR!   want to make sure everything is consistent, use `npm cache verify`
#10 3.253 npm ERR!   instead.  Deleting the cache can only make npm go slower, and is
#10 3.254 npm ERR!   not likely to correct any problems you may be encountering!
#10 3.254 npm ERR! 
#10 3.254 npm ERR!   On the other hand, if you're debugging an issue with the installer,
#10 3.254 npm ERR!   or race conditions that depend on the timing of writing to an empty
#10 3.254 npm ERR!   cache, you can use `npm install --cache /tmp/empty-cache` to use a
#10 3.254 npm ERR!   temporary cache instead of nuking the actual one.
#10 3.254 npm ERR! 
#10 3.254 npm ERR!   If you're sure you want to delete the entire cache, rerun this command
#10 3.254 npm ERR!   with --force.
#10 3.261 
#10 3.261 npm ERR! A complete log of this run can be found in:
#10 3.261 npm ERR!     /root/.npm/_logs/2021-11-22T16_28_56_035Z-debug.log
------
executor failed running [/bin/sh -c npm install && npm cache clean]: exit code: 1
```

Fix `Dockerfile` and try again

```bash
➜ docker build  -t testnode . 
[+] Building 5.4s (13/13) FINISHED                                                                                                                           
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 2.83kB                                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 35B                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/node:17-alpine                                                                                       1.3s
 => [auth] library/node:pull token for registry-1.docker.io                                                                                             0.0s
 => [internal] load build context                                                                                                                       0.0s
 => => transferring context: 1.11kB                                                                                                                     0.0s
 => [1/7] FROM docker.io/library/node:17-alpine@sha256:993bdfb0da7ae8fa4dad7282f797e3e26e88f810d410e0b0409d132d1fb17af3                                 0.0s
 => CACHED [2/7] RUN apk add --update tini                                                                                                              0.0s
 => CACHED [3/7] RUN mkdir -p /usr/src/app                                                                                                              0.0s
 => CACHED [4/7] WORKDIR /usr/src/app                                                                                                                   0.0s
 => CACHED [5/7] COPY package.json package.json                                                                                                         0.0s
 => [6/7] RUN npm install && npm cache clean --force                                                                                                    3.9s
 => [7/7] COPY . .                                                                                                                                      0.0s
 => exporting to image                                                                                                                                  0.1s
 => => exporting layers                                                                                                                                 0.1s
 => => writing image sha256:8de53258fba8e6a1311b258b5008cad249854c2d0edda8145f4bb2ba32b8445e                                                            0.0s
 => => naming to docker.io/library/testnode                                                                                                             0.0s
```
Completed ! But will it run?  Try ... 
`docker container run --rm -p 80:3000 testnode `




