---
layout: post
title: My HandBook of Commands
---

# Cool Tips

> Remember these `cool` (or `hot` or perhaps `fresh`) TIPs

## What is ~ location in Ubuntu

$ cd ~
$ pwd
/home/amit

## When do we use --depth option in git clone ?

- In the context of Continuous Integration (CI) and Continuous Delivery (CD) using git introduces server load. 
- Because Git’s design includes everything in each copy of a repository, every clone gets not only the files but every revision of every file ever committed. 
- It isn’t hard to imagine how that can quickly become a bottleneck with expanding numbers of build, test and deployment pipelines.
- Fortunately, Git supports the notion of a “shallow clone”, which is a more succinctly meaningful way of describing a local repository with history truncated to a particular depth during the clone operation. 
- By providing an argument of --depth 1 to the clone command, the process will copy only the latest revision of everything in the repository. 
- This can be a lifesaver for Git servers that might otherwise be overwhelmed by CI/CD automation.

git clone --depth 1 https://github.com/kubernetes/kubernetes.git kubenetes-git


# My Adventures with Keys

## What - Learn you some KEYs

- The private key is kept on the computer you log in from, 
- The public key is stored on the .ssh/authorized_keys file on all the computers you want to log in to.

- What happens when you log in to a computer via SSH Key ?
  - the SSH server uses the public key to "lock" messages in a way that can only be "unlocked" by your private key 

- As an extra security measure, most SSH programs store the private key in a passphrase-protected format, 
  - so that if your computer is stolen or broken in to, you have enough time to disable your old public key

- Public key authentication is a much better solution than passwords for most people. 
- In fact, if you don't mind leaving a private key unprotected on your hard disk, 
  - you can even use keys to do secure automatic log-ins - as part of a network backup, for example. 

- Different SSH programs generate public keys in different ways, but they all generate public keys in a similar format:
  - <ssh-rsa or ssh-dss> <really long string of nonsense> <username>@<host>

- SSH can use either "RSA" (Rivest-Shamir-Adleman) or "DSA" ("Digital Signature Algorithm") keys. 
  - RSA is the only recommended choice for new keys, so this guide uses "RSA key" and "SSH key" interchangeably.


## Key based SSH login

### How - To create your public and private SSH keys:

mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa

# Aliter: ssh-keygen -t rsa -b 4096
# The default is a 2048 bit key. You can increase this to 4096 bits with the -b flag 


- You will be prompted for a location to save the keys, and a passphrase for the keys. 
- This passphrase will protect your private key while it's stored on the hard drive:

Generating public/private rsa key pair.
Enter file in which to save the key (/home/b/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/b/.ssh/id_rsa.
Your public key has been saved in /home/b/.ssh/id_rsa.pub.

### How - Transfer Client Key to Host

- The key you need to transfer to the host is the public one. 
- If you can log in to a computer over SSH using a password, run the following from your own computer:

```bash

ssh-copy-id <username>@<host>
```

- Aliter: ssh-copy-id "<username>@<host> -p <port_nr>"
- If you are using the standard port 22, you can ignore this tip.
- Aliter : Copy the public key file to the server and concatenate it onto the authorized_keys file manually. 

- It is wise to back that up first:

```bash

cp authorized_keys authorized_keys_Backup
cat id_rsa.pub >> authorized_keys
```

### Verify - Key based SSH login

```bash
ssh <username>@<host>

# You should be prompted for the passphrase for your key:

Enter passphrase for key '/home/<user>/.ssh/id_rsa':
```

- Note : that the host should be configured to allow key based logins

### Trouble - Still it asks for pw

- On the host computer, ensure that the /etc/ssh/sshd_config contains the following lines, 
  - and that they are **uncommented**

```bash
PubkeyAuthentication yes
RSAAuthentication yes
```

- restart OpenSSH, and try logging in again. 
- If you get the passphrase prompt now, then congratulations, you're logging in with a key!

### Trouble - Permission Denied (Public Key)

- Chances are, your /home/<user> or ~/.ssh/authorized_keys permissions are too open by OpenSSH standards. 
- You can get rid of this problem by issuing the following commands:

```bash
chmod go-w ~/
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### Trouble - Agent admitted failure to sign using the key

- This error occurs when the ssh-agent on the client is not yet managing the key.
- Issue the following commands to fix: 

```bash
ssh-add
```

### Trouble - Still more troubles - Wish I could debug ?

- start the sshd in debug mode

```bash
sudo /usr/sbin/sshd -d

ssh -v ( or -vv) username@host
```

### How - To disable password authentication, 

- look for the following line in your sshd_config file:

PasswordAuthentication yes
# replace it with a line that looks like this:
PasswordAuthentication no

- Once you have saved the file and restarted your SSH server, 

### Trouble - I cannot get out of troubles

- Google it
- https://help.ubuntu.com/community/SSH/OpenSSH/Keys
- https://help.ubuntu.com/community/SSH/OpenSSH/Configuring#disable-password-authentication

## Docker is just installed on Ubuntu 14.04 - Observations

- **ip -4 l**

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:1e:67:a0:0b:74 brd ff:ff:ff:ff:ff:ff
3: eth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 00:1e:67:a0:0b:75 brd ff:ff:ff:ff:ff:ff
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:14:ef:45:84 brd ff:ff:ff:ff:ff:ff
```

- **ip -4 addr**

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet 172.16.115.2/16 brd 172.16.255.255 scope global eth0
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
```

- **ip -4 route**

```bash
default via 172.16.1.1 dev eth0
172.16.0.0/16 dev eth0  proto kernel  scope link  src 172.16.115.2
172.17.0.0/16 dev docker0  proto kernel  scope link  src 172.17.0.1
```

### Address already in use

- **netstat with grep - will it help ?**

```bash

netstat -tp | grep 80
tcp        0      0 localhost:48007         localhost:44085         ESTABLISHED 9755/python
tcp        0      0 localhost:44085         localhost:48007         ESTABLISHED 9755/python
```

- **lsof -i :80**

```bash
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2  2424     root    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
apache2 24790 www-data    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
apache2 24791 www-data    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
```

- **lsof -i tcp:80**
`- this is more precise !!!

- **fuser 80/tcp**

```bash
Cannot stat file /proc/26794/fd/4: Stale file handle
Cannot stat file /proc/26794/fd/5: Stale file handle
Cannot stat file /proc/26794/fd/6: Stale file handle
Cannot stat file /proc/26794/fd/7: Stale file handle
Cannot stat file /proc/26794/fd/11: Stale file handle
80/tcp:               2424 24790 24791
```

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
- refer [cheat sheet](https://github.com/wsargent/docker-cheat-sheet)

<br />

### Subnet & Bridge network in Docker

- refer [subnet & bridge](https://github.com/docker/docker/issues/1558)

### Bridge vs. Host mode networking in Docker

- refer [bridge vs. host](http://www.dasblinkenlichten.com/docker-networking-101-host-mode/)

### Networking libs

- refer [tenus](http://containerops.org/2014/07/30/tenus-golang-powered-linux-networking/)
- refer [pipework](https://github.com/jpetazzo/pipework/)

<br />

### lsof: list of open files

- regular file, a directory, a block special file, a character special file, 
- a library, 
- a stream or a network file (Internet socket, NFS file, or UNIX domain socket)\
- has options to exclude kernel blocking paths

<br />

### Linux Server Security - Hardening

- refer [link](https://dzone.com/articles/9-useful-tips-for-linux-server-security?edition=155252)
