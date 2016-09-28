---
layout: post
title: Connect a Docker container from a local network
---

## Connect a Docker container to a local network

### Connect a Docker container to a local network -- Using NAT

refer - [link](http://blog.oddbit.com/2014/08/11/four-ways-to-connect-a-docker/)

#### Step 1 : check your host interface

```bash
# ip route

default via 20.10.1.1 dev eth0  proto static
10.42.0.0/16 dev docker0  proto kernel  scope link  src 10.42.0.1
20.0.0.0/8 dev eth0  proto kernel  scope link  src 20.10.112.151  metric 1
172.17.0.0/16 dev docker0  proto kernel  scope link  src 172.17.0.1


# ip addr show eth0

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 50:46:5d:ac:8e:fe brd ff:ff:ff:ff:ff:ff
    inet 20.10.112.151/8 brd 20.255.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5246:5dff:feac:8efe/64 scope link
       valid_lft forever preferred_lft forever

# ifconfig

eth0      Link encap:Ethernet  HWaddr 50:46:5d:ac:8e:fe
          inet addr:20.10.112.151  Bcast:20.255.255.255  Mask:255.0.0.0
          inet6 addr: fe80::5246:5dff:feac:8efe/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2891663 errors:0 dropped:333 overruns:0 frame:0
          TX packets:167745 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:645669170 (645.6 MB)  TX bytes:49992203 (49.9 MB)
```

#### Step 2 : Assign a target address to your host interface

```bash

# e.g. 20.10.113.2/24 is the address which we want to communicate to a docker container

ip addr add 20.10.113.2/24 dev eth0

```

#### Step 3 : Set the docker compose as below & specific settings to set up concourse

refer [link](https://concourse.ci/docker-repository.html)

```bash

# echo $CONCOURSE_EXTERNAL_URL
http://20.10.113.2:8181
```

```yml
concourse-db:
  image: postgres:9.5
  environment:
    POSTGRES_DB: concourse
    POSTGRES_USER: concourse
    POSTGRES_PASSWORD: changeme
    PGDATA: /database

concourse-web:
  image: concourse/concourse
  links: [concourse-db]
  command: web
  ports: ["20.10.113.2:8181:8181"]
  volumes: ["/home/concourse/keys/web:/concourse-keys"]
  environment:
    CONCOURSE_BASIC_AUTH_USERNAME: admin
    CONCOURSE_BASIC_AUTH_PASSWORD: test
    CONCOURSE_EXTERNAL_URL: "${CONCOURSE_EXTERNAL_URL}"
    CONCOURSE_POSTGRES_DATA_SOURCE: |-
      postgres://concourse:changeme@concourse-db:5432/concourse?sslmode=disable

concourse-worker:
  image: concourse/concourse
  privileged: true
  links: [concourse-web]
  command: worker
  volumes: ["/home/concourse/keys/worker:/concourse-keys"]
  environment:
    CONCOURSE_TSA_HOST: concourse-web
```

#### Step 4 : Verify docker

```bash

# ip addr show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 50:46:5d:ac:8e:fe brd ff:ff:ff:ff:ff:ff
    inet 20.10.112.151/8 brd 20.255.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet 20.10.113.2/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5246:5dff:feac:8efe/64 scope link
       valid_lft forever preferred_lft forever

# ip route
default via 20.10.1.1 dev eth0  proto static
10.42.0.0/16 dev docker0  proto kernel  scope link  src 10.42.0.1
20.0.0.0/8 dev eth0  proto kernel  scope link  src 20.10.112.151  metric 1
20.10.113.0/24 dev eth0  proto kernel  scope link  src 20.10.113.2
172.17.0.0/16 dev docker0  proto kernel  scope link  src 172.17.0.1

# brctl show docker0
bridge name     bridge id               STP enabled     interfaces
docker0         8000.0242e8f3842f       no              veth3558646
                                                        veth85e5297
                                                        vethe78680b
                                                        vethfb4357b
                                                        vethfd167e2


# ip addr show docker0
5: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:e8:f3:84:2f brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
    inet 10.42.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:e8ff:fef3:842f/64 scope link
       valid_lft forever preferred_lft forever

# docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                                          NAMES
5c1c95135f22        concourse/concourse             "/usr/local/bin/dumb-"   11 minutes ago      Up 10 minutes                                                      concourse_concourse-worker_1
732a9ff13532        concourse/concourse             "/usr/local/bin/dumb-"   11 minutes ago      Up 10 minutes       20.10.113.2:8181->8181/tcp                     concourse_concourse-web_1
d0d13fcad8f7        postgres:9.5                    "/docker-entrypoint.s"   28 hours ago        Up 10 minutes       5432/tcp                                       concourse_concourse-db_1

# docker exec concourse_concourse-web_1 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
48: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:05 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.5/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:5/64 scope link
       valid_lft forever preferred_lft forever

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
6    MASQUERADE  tcp  --  172.17.0.5           172.17.0.5           tcp dpt:8181

Chain CATTLE_POSTROUTING (1 references)
num  target     prot opt source               destination
1    ACCEPT     all  --  10.42.0.0/16         169.254.169.250
2    MASQUERADE  tcp  --  10.42.0.0/16        !10.42.0.0/16         masq ports: 1024-65535
3    MASQUERADE  udp  --  10.42.0.0/16        !10.42.0.0/16         masq ports: 1024-65535
4    MASQUERADE  all  --  10.42.0.0/16        !10.42.0.0/16
5    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x2d3a7 to:10.42.185.255
6    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x1214b to:10.42.74.59
7    MASQUERADE  tcp  --  172.17.0.0/16        0.0.0.0/0            masq ports: 1024-65535
8    MASQUERADE  udp  --  172.17.0.0/16        0.0.0.0/0            masq ports: 1024-65535

Chain CATTLE_PREROUTING (1 references)
num  target     prot opt source               destination
1    DNAT       tcp  --  10.42.0.0/16         10.42.0.1            tcp dpt:53 to:169.254.169.250
2    DNAT       udp  --  10.42.0.0/16         10.42.0.1            udp dpt:53 to:169.254.169.250
3    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:C2:7D:9F MARK set 0x2d3a7
4    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:AE:3B:F7 MARK set 0x1214b

Chain DOCKER (2 references)
num  target     prot opt source               destination
1    RETURN     all  --  0.0.0.0/0            0.0.0.0/0
2    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.17.0.2:8080
3    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:4500 to:172.17.0.3:4500
4    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:500 to:172.17.0.3:500
5    DNAT       tcp  --  0.0.0.0/0            20.10.113.2          tcp dpt:8181 to:172.17.0.5:8181

# lsof -i tcp:8181
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker-pr 5185 root    4u  IPv4 584192      0t0  TCP 20.10.113.2:8181 (LISTEN)

# netstat -ntlpe
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode       PID/Program name
tcp        0      0 0.0.0.0:3389            0.0.0.0:*               LISTEN      122        17458       2754/xrdp
tcp        0      0 127.0.0.1:9344          0.0.0.0:*               LISTEN      0          484131      31531/.cadvisor
tcp        0      0 0.0.0.0:516             0.0.0.0:*               LISTEN      0          10612       1102/rsyslogd
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      119        16867       1544/mysqld
tcp        0      0 127.0.0.1:11211         0.0.0.0:*               LISTEN      117        9790        1559/memcached
tcp        0      0 20.10.113.2:8181        0.0.0.0:*               LISTEN      0          584192      5185/docker-proxy
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN      0          9805        1623/dnsmasq
tcp        0      0 127.0.0.1:3350          0.0.0.0:*               LISTEN      0          17448       2756/xrdp-sesman
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      0          11935       1450/sshd
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      0          13158       2517/cupsd
tcp        0      0 0.0.0.0:52921           0.0.0.0:*               LISTEN      118        10838       2228/beam.smp
tcp        0      0 0.0.0.0:3260            0.0.0.0:*               LISTEN      0          9745        1523/tgtd
tcp6       0      0 :::35357                :::*                    LISTEN      0          19578       2927/apache2
tcp6       0      0 :::516                  :::*                    LISTEN      0          10613       1102/rsyslogd
tcp6       0      0 :::5000                 :::*                    LISTEN      0          19574       2927/apache2
tcp6       0      0 :::5672                 :::*                    LISTEN      118        14239       2228/beam.smp
tcp6       0      0 :::80                   :::*                    LISTEN      0          19568       2927/apache2
tcp6       0      0 :::8080                 :::*                    LISTEN      0          17456       2786/docker-proxy
tcp6       0      0 :::4369                 :::*                    LISTEN      118        13964       1687/epmd
tcp6       0      0 :::22                   :::*                    LISTEN      0          11937       1450/sshd
tcp6       0      0 ::1:631                 :::*                    LISTEN      0          13157       2517/cupsd
tcp6       0      0 :::3260                 :::*                    LISTEN      0          9746        1523/tgtd

# ping 20.10.113.2 from any host in the 20.10.1.1 network works

# However, the url was not accessible on port 8181
```

#### Step 5 : Change the docker compose yml file to set network = host

- refer [link](https://docs.docker.com/compose/compose-file/#/network-mode)

```

# docker-compose up -d
Recreating concourse_concourse-db_1
Recreating concourse_concourse-web_1

ERROR: for concourse-web  Cannot create container for service concourse-web: Conflicting options: host type networking can't be used with links. This would result in undefined behavior
ERROR: Encountered errors while bringing up the project.
```



#### Step 6 : Add a rule that allows port 8181 traffic through iptables

```bash
iptables -I INPUT -p tcp -m tcp --dport 8181 -j ACCEPT

# iptables -L -n --line-numbers

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8181
2    w--input   all  --  0.0.0.0/0            0.0.0.0/0

```


#### Step 7 : Expose the container's port on your localhosts port through iptables

```bash
iptables -t nat -A DOCKER -p tcp --dport <LOCALHOSTPORT> -j DNAT --to-destination <CONTAINERIP>:<PORT>

# iptables -t nat -A DOCKER -p tcp --dport 8181 -j DNAT --to-destination 172.17.0.5:8181

# iptables -L -n --line-numbers -t nat

Chain DOCKER (2 references)
num  target     prot opt source               destination
1    RETURN     all  --  0.0.0.0/0            0.0.0.0/0
2    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.17.0.2:8080
3    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:4500 to:172.17.0.3:4500
4    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:500 to:172.17.0.3:500
5    DNAT       tcp  --  0.0.0.0/0            20.10.113.2          tcp dpt:8181 to:172.17.0.5:8181
6    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8181 to:172.17.0.5:8181

```

#### Summary

- All the above steps did not help in accessing the Concourse CI web from my laptop
- It was found that steps 5, 6 & 7 were not required.
- Concourse somehow needed 8080 to listen for its web
  - We had to install nestat into concourse web container to check the listening port
- Hence step 3 was modified to have the container use port 8080
  - original - ports: ["20.10.113.2:8181:8181"]
  - modified - ports: ["20.10.113.2:8181:8080"]
- Further the changes to iptables were removed to have below:

```bash

# iptables -L -n --line-numbers -t nat
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    CATTLE_PREROUTING  all  --  0.0.0.0/0            0.0.0.0/0
2    DOCKER     all  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
3    w--prerouting  all  --  0.0.0.0/0            0.0.0.0/0

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination
1    DOCKER     all  --  0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL
2    w--prerouting  all  --  0.0.0.0/0            0.0.0.0/0

Chain POSTROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    CATTLE_POSTROUTING  all  --  0.0.0.0/0            0.0.0.0/0
2    MASQUERADE  all  --  172.17.0.0/16        0.0.0.0/0
3    MASQUERADE  tcp  --  172.17.0.2           172.17.0.2           tcp dpt:8080
4    MASQUERADE  udp  --  172.17.0.3           172.17.0.3           udp dpt:4500
5    MASQUERADE  udp  --  172.17.0.3           172.17.0.3           udp dpt:500
6    w--postrouting  all  --  0.0.0.0/0            0.0.0.0/0
7    MASQUERADE  tcp  --  172.17.0.5           172.17.0.5           tcp dpt:8080

Chain CATTLE_POSTROUTING (1 references)
num  target     prot opt source               destination
1    ACCEPT     all  --  10.42.0.0/16         169.254.169.250
2    MASQUERADE  tcp  --  10.42.0.0/16        !10.42.0.0/16         masq ports: 1024-65535
3    MASQUERADE  udp  --  10.42.0.0/16        !10.42.0.0/16         masq ports: 1024-65535
4    MASQUERADE  all  --  10.42.0.0/16        !10.42.0.0/16
5    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x2d3a7 to:10.42.185.255
6    SNAT       all  -- !10.42.0.0/16         169.254.169.250      mark match 0x1214b to:10.42.74.59
7    MASQUERADE  tcp  --  172.17.0.0/16        0.0.0.0/0            masq ports: 1024-65535
8    MASQUERADE  udp  --  172.17.0.0/16        0.0.0.0/0            masq ports: 1024-65535

Chain CATTLE_PREROUTING (1 references)
num  target     prot opt source               destination
1    DNAT       tcp  --  10.42.0.0/16         10.42.0.1            tcp dpt:53 to:169.254.169.250
2    DNAT       udp  --  10.42.0.0/16         10.42.0.1            udp dpt:53 to:169.254.169.250
3    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:C2:7D:9F MARK set 0x2d3a7
4    MARK       all  -- !10.42.0.0/16         169.254.169.250      MAC 02:E2:1E:AE:3B:F7 MARK set 0x1214b

Chain DOCKER (2 references)
num  target     prot opt source               destination
1    RETURN     all  --  0.0.0.0/0            0.0.0.0/0
2    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.17.0.2:8080
3    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:4500 to:172.17.0.3:4500
4    DNAT       udp  --  0.0.0.0/0            0.0.0.0/0            udp dpt:500 to:172.17.0.3:500
5    DNAT       tcp  --  0.0.0.0/0            20.10.113.2          tcp dpt:8181 to:172.17.0.5:8080

Chain w--postrouting (1 references)
num  target     prot opt source               destination

Chain w--prerouting (2 references)
num  target     prot opt source               destination
```
- Now access the concourse web by http://20.10.113.2:8181
