- ex_1879060837_container_cli_testing
	- Created on the: 2021-11-11 14:28
	- Review date: 2022-11-1
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
	- Author Notes: 
	- Tags: 
		-  #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# Excercise - CLI App Testing
Requirements ⚓ 
- Know how to use `-it` to get shell in container
- Understand basics of what a Linux distribution is like Ubuntu
and CentOS
-  Know how to run a container

Learning AIM 🎯 
- How easy it is to get into a distro and start testing.... No waiting here ...

 Exercise 🤸
- Use different Linux distro containers to check curl cli tool version
- Use two different terminal windows to start bash in both centos:7
and ubuntu:14.04, using -it
- Learn the docker container run —rm option so you can save
cleanup
- Ensure curl is installed and on latest version for that distro
- ubuntu: apt-get update && apt-get install curl • centos: yum update curl
- Check curl --version

Don't cheat...

--- 

## Steps 
Ok, here are the steps I have undertaken to complete this exercise. 
Ubuntu
```bash
docker container run -it --rm --name ubuntu14 ubuntu 
root@c0ad53fe33a1:/# apt-get update && apt-get install curl
root@c0ad53fe33a1:/# curl --version
curl 7.68.0 (aarch64-unknown-linux-gnu) libcurl/7.68.0 OpenSSL/1.1.1f zlib/1.2.11 brotli/1.0.7 libidn2/2.2.0 libpsl/0.21.0 (+libidn2/2.2.0) libssh/0.9.3/openssl/zlib nghttp2/1.40.0 librtmp/2.3
Release-Date: 2020-01-08
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtmp rtsp scp sftp smb smbs smtp smtps telnet tftp 
Features: AsynchDNS brotli GSS-API HTTP2 HTTPS-proxy IDN IPv6 Kerberos Largefile libz NTLM NTLM_WB PSL SPNEGO SSL TLS-SRP UnixSockets
```
CentOS
```bash 
docker container run -it --rm --name centos7 centos:7 
[root@46355a0a718a /]# yum update curl
[root@46355a0a718a /]# curl --version
curl 7.29.0 (aarch64-redhat-linux-gnu) libcurl/7.29.0 NSS/3.53.1 zlib/1.2.7 libidn/1.28 libssh2/1.8.0
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp scp sftp smtp smtps telnet tftp 
Features: AsynchDNS GSS-Negotiate IDN IPv6 Largefile NTLM NTLM_WB SSL libz unix-sockets 
```

