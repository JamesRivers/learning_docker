Notes on the install and configuration of the wireguard server for the homelab in which the aviator versio set up is being constructed. 

## Install - ARM 64
- on pi `192.168.1.149` - `docker pull linuxserver/wireguard`
- docker compose 

```yml
---
version: "2.1"
services:
  wireguard:
    image: ghcr.io/linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1001
      - PGID=1001
      - TZ=Europe/London
      - SERVERURL=auto
      - SERVERPORT=51820 
      - PEERS=1 
      - PEERDNS=8.8.8.8
      - INTERNAL_SUBNET=10.13.13.0 #optional
      - ALLOWEDIPS=0.0.0.0/0, 192.168.1.0/24 #optional
    volumes:
      - /home/james/dockerconfig/wireguard-server/config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped
```
- docker-compose up
- collect peer1.conf from `/home/james/dockerconfig/wireguard-server/config/peer1`
```conf
[Interface]
Address = 10.13.13.2
PrivateKey = secret
ListenPort = 51820
DNS = 8.8.8.8

[Peer]
PublicKey = secret
Endpoint = 94.0.247.18:51820
AllowedIPs = 0.0.0.0/0, 192.168.1.0/24
```
copy the peer1.conf to the client machine at 
`/etc/wireguard/wg0.conf`
obvs install wireguard on the client machine. Note you can install the wireguard application on the phone and scan the peer1.png located at - `/home/james/dockerconfig/wireguard-server/config/peer1`.
- Check the handshake `docker exec -it wireguard wg`
```bash
docker exec -it wireguard wg
interface: wg0
  public key: secret
  private key: (hidden)
  listening port: 51820

peer: secret
  endpoint: 148.252.133.149:36447
  allowed ips: 10.13.13.2/32
  latest handshake: 1 minute, 50 seconds ago
  transfer: 14.10 MiB received, 155.64 MiB sent
```
### Tests from remote
- can i ping the 192.168.1x subnet - yes
- can i ping the 172.16.x.x subnet - no

#### Changes 
Cisco Switchport on connected pi. 
- fa 0/2 
	- changed to trunk port 
	- allowed vlan 1(native 192.168.1.x) and vlan 2 (172.16)
- Pi netplan changes
```yml
network:
    version: 2
    ethernets:
        eth0:
            addresses:
                - 192.168.1.149/24
            gateway4: 192.168.1.1
            nameservers:
                addresses: [8.8.8.8,1.1.1.1]
        eth0: {}

    vlans:
        vlan.2:
            id: 2
            link: eth0
            addresses: [172.16.0.149/16]
```

from cisco can I 
- ping 192.168.1.149
	- yes
```cisco
wyc_3560b>ping 192.168.1.149

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.149, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```
- ping 172.16.0.149
	- yes
```cisco
wyc_3560b>ping 172.16.0.149 

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.0.149, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
```
- From remote can i ping 172.16.0.1? 
	- yes!

### Commands to start wireguard on client
- up `wg-quick up wg0`
- down `wg-quick down wg0`
