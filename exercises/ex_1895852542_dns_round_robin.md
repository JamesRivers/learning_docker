- ex_1895852542_dns_round_robin
	- Created on the: 2021-11-11 16:56
	- Review date: 2022-11-11 
	- Updated:
	- Written by 
		- James Rivers
	- Written against (version):
		- Client 20.10.5
		- Server 20.10.5
	- Sources: 
	- Author Notes: 
	- Tags: 
		- #docker 
	- ICON set : 
		- Warning ⚠️ - Important need to know / Notes 🗒 - Additional notes / Version 🌱 - Specific to a Version / Knowledge 🧠 - Did you know / WWW 🕸 - Links to web pages / Learning AIM 🎯 - The Learning target for this topic / Evaluation 🧪 - Set of evaluation items to show the target was reached / Exercise 🤸 - Hands on time... /  Reading 📚  - Items for you to read / Requirements ⚓ before you start you need to know.
---
# exercise - dns round robin
Requirements ⚓ 
- Know how to use `-it` to get shell in container
- Understand basics of what a Linux distribution is like Ubuntu
and CentOS
-  Know how to run a container
-  Understand basics of DNS records

Learning AIM 🎯
- DNS round robin testing in docker - same DNS name

Exercise 🤸
 - Ever since Docker Engine 1.11, we can have multiple containers on a created network respond to the same DNS address
- Create a new virtual network (default bridge driver)
- Create two containers from elasticsearch:2 image
- Research and use —network-alias search when creating them to give them an additional DNS name to respond to
- Run alpine nslookup search with --net to see the two containers list for the same DNS name
- Run centos curl -s search:9200 with --net multiple times until you see both "name" fields show

---
## steps
Build the network and create the 2 elasticsearch containers 
```bash 
❯ docker network create --driver bridge newdns-network 
474c292289ba3e1101db8ed951be77df3f6b70fb49a7a87a74a912e11c345934
docker container run -d --net newdns-network -e discovery.type=single-node --net-alias search elasticsearch:7.14.2
docker container run -d --net newdns-network -e discovery.type=single-node --net-alias search elasticsearch:7.14.2
```

Knowledge 🧠 What is elasticsearch?  Elasticsearch is a search engine based on the Lucene library. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. More info [here](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjtuZGA6ZD0AhUzQUEAHXXbAOAQmhN6BAg8EAI&url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FElasticsearch&usg=AOvVaw0djJYNuxlBs6k1rD2t_on1)

Next we need to run an nslookup. 
```bash
docker container run --rm --net newdns-network alpine nslookup search  
```

Next we need to run curl and check the result
```bash 
docker container run --rm --net newdns-network centos curl -s search:9200
```

