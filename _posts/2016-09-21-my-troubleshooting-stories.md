---
layout: post
title: My Troubleshooting Stories
---

## Troubleshooting Stories - Some of these are curated !!!

### Address already in use

```bash

Error (500 Server Error: Internal Server Error ("driver failed programming external connectivity on endpoint 
r-wekan_wekan_1 (d498ed937dbd3490b05c5e89251fa1073071412cb0e2a4e5606e3cf16e48bfc6): Error starting userland 
proxy: listen tcp 0.0.0.0:80: bind: address already in use")
```

<br />

```bash
# netstat with grep will not help

netstat -tp | grep 80
tcp        0      0 localhost:48007         localhost:44085         ESTABLISHED 9755/python
tcp        0      0 localhost:44085         localhost:48007         ESTABLISHED 9755/python

# use lsof
lsof -i :80
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2  2424     root    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
apache2 24790 www-data    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
apache2 24791 www-data    4u  IPv6  16600      0t0  TCP *:http (LISTEN)

# more precise command
lsof -i tcp:80

# Alternative: use fuser
fuser 80/tcp
Cannot stat file /proc/26794/fd/4: Stale file handle
Cannot stat file /proc/26794/fd/5: Stale file handle
Cannot stat file /proc/26794/fd/6: Stale file handle
Cannot stat file /proc/26794/fd/7: Stale file handle
Cannot stat file /proc/26794/fd/11: Stale file handle
80/tcp:               2424 24790 24791
```

<br />

### Do you understand netstat ?

- reverse dns lookup for **faster output**
  - use -n
  - no need to find hostname for each IP
  - IP address is sufficient
- listening connections
  - use -l
  - netstat -nl
- get name/pid and user id
  - use -p
  - netstat -nlpt
- get extended info (e.g. owner) of the pid
  - use -e
  - netstat -nlpte
- get kernel routing info
  - use -r
  - netstat -rn

### Do you understand iptables ?

```bash
# iptables -L -n --line-numbers -t nat
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    CATTLE_PREROUTING  all  --  0.0.0.0/0            0.0.0.0/0
2    DOCKER     all  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination
1    DOCKER     all  --  0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    CATTLE_POSTROUTING  all  --  0.0.0.0/0            0.0.0.0/0
2    MASQUERADE  all  --  172.17.0.0/16        0.0.0.0/0
3    MASQUERADE  tcp  --  172.17.0.2           172.17.0.2           tcp dpt:8080
4    MASQUERADE  udp  --  172.17.0.3           172.17.0.3           udp dpt:4500
5    MASQUERADE  udp  --  172.17.0.3           172.17.0.3           udp dpt:500
6    MASQUERADE  tcp  --  172.17.0.6           172.17.0.6           tcp dpt:8181

Chain CATTLE_POSTROUTING (1 references)
num  target     prot opt source               destination
1    ACCEPT     all  --  10.42.0.0/16         169.254.169.250
2    MASQUERADE  tcp  --  10.42.0.0/16        !10.42.0.0/16         masq ports: 1024-65535
3    MASQUERADE  udp  --  10.42.0.0/16        !10.42.0.0/16         masq ports: 1024-65535
4    MASQUERADE  all  --  10.42.0.0/16        !10.42.0.0/16
5    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x2d3a7 to:10.42.185.255
6    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x1214b to:10.42.74.59
7    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x18b34 to:10.42.101.172
8    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x376ce to:10.42.227.22
9    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x22ba6 to:10.42.142.246
10   MASQUERADE  tcp  --  172.17.0.0/16        0.0.0.0/0            masq ports: 1024-65535
11   MASQUERADE  udp  --  172.17.0.0/16        0.0.0.0/0            masq ports: 1024-65535

Chain CATTLE_PREROUTING (1 references)
num  target     prot opt source               destination
1    MARK       tcp  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL tcp dpt:8181 MARK set 0x668a0
2    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL tcp dpt:8181 to:10.42.227.22:8181
3    DNAT       tcp  --  10.42.0.0/16         10.42.0.1            tcp dpt:53 to:169.254.169.250
4    DNAT       udp  --  10.42.0.0/16         10.42.0.1            udp dpt:53 to:169.254.169.250
5    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:C2:7D:9F MARK set 0x2d3a7
6    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:AE:3B:F7 MARK set 0x1214b
7    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:6A:DA:1B MARK set 0x18b34
8    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:0D:60:78 MARK set 0x376ce
9    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:3D:CD:A6 MARK set 0x22ba6

Chain DOCKER (2 references)
num  target     prot opt source               destination
1    RETURN     all  --  0.0.0.0/0            0.0.0.0/0
2    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.17.0.2:8080
3    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:4500 to:172.17.0.3:4500
4    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:500 to:172.17.0.3:500
5    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8181 to:172.17.0.6:8181
```

<br />

### Do you understand route ?

- You can learn the subnet for the interface
- You get to know the default gateway
- Hence the networkid part & the hostid part from an IP address is also known

```bash
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         20.10.1.1       0.0.0.0         UG    0      0        0 eth0
10.42.0.0       0.0.0.0         255.255.0.0     U     0      0        0 docker0
20.0.0.0        0.0.0.0         255.0.0.0       U     1      0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0

# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         20.10.1.1       0.0.0.0         UG    0      0        0 eth0
10.42.0.0       *               255.255.0.0     U     0      0        0 docker0
20.0.0.0        *               255.0.0.0       U     1      0        0 eth0
172.17.0.0      *               255.255.0.0     U     0      0        0 docker0

# ip route show
default via 20.10.1.1 dev eth0  proto static
10.42.0.0/16 dev docker0  proto kernel  scope link  src 10.42.0.1
20.0.0.0/8 dev eth0  proto kernel  scope link  src 20.10.112.151  metric 1
172.17.0.0/16 dev docker0  proto kernel  scope link  src 172.17.0.1
```

<br />

### subnet - sub networking

- refer [link](http://searchnetworking.techtarget.com/definition/subnet)
- divide a network into subnets
- allows it to be connected to the internet with a single shared network address
- the 32-bit IP address has 2 parts
  - part identifying the network no
  - part identifying the machine within the network
  - e.g.  130.5.5.25
    - <--Network address--><--Host address--> 130.5 . 5.25
  - we can use some its in the specific machine to identify a specific subnet
  - <--Network address--><--Subnet address--><--Host address--> 130.5 . 5 . 25
  - here we have divided the subnet into 8 bits

<br />

### subnet - Again

- Assume a Class A network (i.e mask of 255.0.0.0)  as shown below:
  - IP     = 8.20.15.1  = 00001000.00010100.00001111.00000001
  - Mask = 255.0.0.0 = 11111111.00000000.00000000.00000000
- TIP = All the ip bits that are covered by 1's of the mask represent network
- TIP = All the ip bits that are covered by 0's of the mask represent host/node
- Hence network id = 8
- Hence host id = 20.15.1

- Now lets subnet
  - Why ?
  - else you can use only one network within a Class !!!
  - each data link on a network must have a unique network ID
  - breaking into subnets allows a unique network/subnet ID

<br />

#### What are bridge interfaces ?

- if a network host has 2 NICs it can bridge 2 segments of a network together
  - connect 2 network segments together
- bridge forwards packets on the MAC address
- used a lot in virtualization
- bridge is a virtual interface mapped to corresponding physical interfaces
- e.g. 2 network cards; eth0 & eth1
  - bridge is connected to eth0 & eth1 & named as br0 & assign an IP to br0
- can be managed via /etc/sysconfig/network-script
- actual purpose was to provide network connectivity failover & avoid downtime
- can convert the single physical interface to a bridge network


```bash
service NetworkManager stop

chkconfig NetworkManager off

cd /etc/sysconfig/network-scripts

vi ifcfg-br0

vi ifcfg-eth0

vi ifcfg-eth1

service network restart
```

<br />

```bash

# on ubuntu trusty

sudo apt-get install bridge-utils

vi /etc/network/interfaces

sudo reboot

ifconfig
```

<br />

### brctl

- ethernet bridge administration
- used to connect different networks of ethernets together
  - s.t. these ethernets appear as one ethernet to the apps
- **each ethernet** being connected to the bridge corresponds to **1 physical interface** in the bridge
- each bridge has a number of **ports attached** to it
  - traffic coming in on any of these ports will be forwarded to the other ports
  - this forwarding is transparent
  - this is invisible to rest of network, i.e. bridge will not show in traceroute
- NOTES:
  - the ethernet address location data is not static data
  - machines can move to other ports
  - network cards can be replaced i.e. change of machine's ethernet address
- Multiple bridges can work together to create even larger networks of ethernet
  - using IEEE 802.1d ```Spanning Tree Protocol (STP)```

```bash

brctl addbr <name>
brctl addif <brname> <ifname>

# Above will make the interface <ifname> a port of the bridge <brname>
# All frames received on <ifname> will be destined to <brname>
# Also sending frames on <brname> will be destined to <ifname>

brctl show <brname>
brctl showmacs <brname>

```

<br />

### Change IP Subnet of docker's docker0 bridge network

- refer [gist](https://gist.github.com/kamermans/94b1c41086de0204750b)
- refer [post](https://support.zenoss.com/hc/en-us/articles/203582809-How-to-Change-the-Default-Docker-Subnet)
- refer [via brctl & docker -b option](https://docs.docker.com/engine/userguide/networking/default_network/build-bridges/)

<br />

### Subnet & Bridge network in Docker

- refer [subnet & bridge](https://github.com/docker/docker/issues/1558)

### Bridge vs. Host mode networking in Docker

- refer [bridge vs. host](http://www.dasblinkenlichten.com/docker-networking-101-host-mode/)

<br />

### lsof: list of open files

- regular file, a directory, a block special file, a character special file, 
- a library, 
- a stream or a network file (Internet socket, NFS file, or UNIX domain socket)\
- has options to exclude kernel blocking paths

<br />

### Linux Server Security - Hardening

- refer [link](https://dzone.com/articles/9-useful-tips-for-linux-server-security?edition=155252)
