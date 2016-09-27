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

### Do you understand this ?

- iptables -L -n --line-numbers -t nat

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

### Rancher with curl

- refer [link](https://github.com/rancher/rancher/issues/3422)

```bash

META_URL="http://rancher-metadata/2015-07-25"

get_host_ip() {
    UUID=$(curl -s -H 'Accept: application/json' ${META_URL}/containers/${1}|jq -r '.host_uuid')
    IP=$(curl -s -H 'Accept: application/json' ${META_URL}/hosts |jq -r ".[] | select(.uuid==\"${UUID}\") | .agent_ip")
    echo ${IP}
}

get_host_name() {
    UUID=$(curl -s -H 'Accept: application/json' ${META_URL}/containers/${1}|jq -r '.host_uuid')
    IP=$(curl -s -H 'Accept: application/json' ${META_URL}/hosts |jq -r ".[] | select(.uuid==\"${UUID}\") | .name")
    echo ${IP}
}

get_container_primary_ip() {
    IP=$(curl -s -H 'Accept: application/json' ${META_URL}/containers/${1}|jq -r .primary_ip)
    echo ${IP}
}

get_self_name() {
    self=$(curl -s -H 'Accept: application/json' ${META_URL}/self/container| jq -r .name)
    echo ${self}
}

# then to get the current ip of the running container:
IP=$(get_container_primary_ip get_self_name)
```

<br />

### lsof: list of open files

- regular file, a directory, a block special file, a character special file, 
- a library, 
- a stream or a network file (Internet socket, NFS file, or UNIX domain socket)\
- has options to exclude kernel blocking paths

<br />

### Linux Server Security - Hardening

- refer [link](https://dzone.com/articles/9-useful-tips-for-linux-server-security?edition=155252)
