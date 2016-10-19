---
layout: post
title: Kubernetes Travels - The installation story
---

# Kubernetes Travels

> This can be thought of a book that meanders across the tool (or river) called 
Kubernetes (*of the size of Amazon*). This book has been divided across many chapters.
The chapter 1 puts some details around a basic cluster installation.

## Chapter 1 - Kicking the installation boat

> These steps are being tried on 3 Ubuntu 14.04 boxes. 1 box will be kube master while 
other 2 boxes will be kube nodes.

- sudo apt install bridge-utils
- install docker
- download the kubernetes latest release tar from its github releases page

> I am running these in the master box

- tar -xvf the.tar.gz
- edit the cluster/ubuntu/config-default.sh
  - service - 192.16.1.0/24 - should not conflict with flannel
  - flannel - 182.16.0.0/16 - should be a private ip
  - dns server - 192.16.1.10

> I guess everything is ready to set up my first ever attempt to create kubernetes cluster.

- Try now.

```
$ KUBERNETES_PROVIDER=ubuntu ./kube-up.sh
... Starting cluster using provider: ubuntu
... calling verify-prereqs
Could not find or add an SSH identity.
Please start ssh-agent, add your identity, and retry.
```

> Hmmn, I forgot to do implement *All the remote servers can be ssh logged in without a password by using 
key authentication.*

> I ran thorugh the required steps to create a key based ssh login from master node to worker nodes. 
I am ready I suppose.

- Try 2

```
$ KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

... Starting cluster using provider: ubuntu
... calling verify-prereqs
Identity added: /home/amit/.ssh/id_rsa (/home/amit/.ssh/id_rsa)
... calling kube-up
~/kubernetes/cluster/ubuntu ~/kubernetes/cluster
Prepare flannel 0.5.5 release ...
./../cluster/../cluster/ubuntu/download-release.sh: line 37: curl: command not found
```

> Well, this box is a Ubuntu 14.04 server & has got the pre-requisites installed. However, curl
seems to be a prerequisite. No problem I love curl.

- sudo apt-get install curl

```bash
KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

... Starting cluster using provider: ubuntu
... calling verify-prereqs
Identity added: /home/amit/.ssh/id_rsa (/home/amit/.ssh/id_rsa)
... calling kube-up
~/kubernetes/cluster/ubuntu ~/kubernetes/cluster
Prepare flannel 0.5.5 release ...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   608    0   608    0     0    543      0 --:--:--  0:00:01 --:--:--   543
100 3408k  100 3408k    0     0   309k      0  0:00:10  0:00:10 --:--:--  788k
Prepare etcd 2.3.1 release ...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   606    0   606    0     0    567      0 --:--:--  0:00:01 --:--:--   567
100 7946k  100 7946k    0     0  1249k      0  0:00:06  0:00:06 --:--:-- 1763k
Prepare kubernetes 1.4.3 release ...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   593    0   593    0     0    553      0 --:--:--  0:00:01 --:--:--   553
 37 1007M   37  381M    0     0   978k      0  0:17:35  0:06:39  0:10:56     0
```

> My servers had a network flip. I have to resart the process once again. Hope it
understands to start from where it left before.

```bash
KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

... Starting cluster using provider: ubuntu
... calling verify-prereqs
Identity added: /home/amit/.ssh/id_rsa (/home/amit/.ssh/id_rsa)
... calling kube-up
~/kubernetes/cluster/ubuntu ~/kubernetes/cluster
Prepare flannel 0.5.5 release ...
Prepare etcd 2.3.1 release ...
Prepare kubernetes 1.4.3 release ...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   593    0   593    0     0    540      0 --:--:--  0:00:01 --:--:--   541
100 1007M  100 1007M    0     0  3897k      0  0:04:24  0:04:24 --:--:-- 3754k
~/kubernetes/cluster/ubuntu/kubernetes/server ~/kubernetes/cluster/ubuntu ~/kubernetes/cluster
~/kubernetes/cluster/ubuntu ~/kubernetes/cluster
Done! All your binaries locate in kubernetes/cluster/ubuntu/binaries directory
~/kubernetes/cluster

Deploying master and node on machine 172.16.115.2
amit@172.16.115.2's password:
```

> I attended my prompt very late. However, why did it ask for password ? Do I need to make the master 
machine login to itself without a password but by key. Remember my master node is the node where I
am trying this out.

> I will make my master node ssh to itself via key.

```bash
KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

# .. earlier steps
Deploying master and node on machine 172.16.115.2
saltbase/salt/generate-cert/make-ca-cert.sh: No such file or directory
easy-rsa.tar.gz                                                                                                    100%   42KB  42.4KB/s   00:00
config-default.sh                                                                                                  100% 5587     5.5KB/s   00:00
util.sh                                                                                                            100%   29KB  28.9KB/s   00:00
kubelet.conf                                                                                                       100%  645     0.6KB/s   00:00
kube-proxy.conf                                                                                                    100%  688     0.7KB/s   00:00
kubelet                                                                                                            100% 2158     2.1KB/s   00:00
kube-proxy                                                                                                         100% 2233     2.2KB/s   00:00
kube-controller-manager.conf                                                                                       100%  761     0.7KB/s   00:00
etcd.conf                                                                                                          100%  707     0.7KB/s   00:00
kube-scheduler.conf                                                                                                100%  682     0.7KB/s   00:00
kube-apiserver.conf                                                                                                100%  682     0.7KB/s   00:00
etcd                                                                                                               100% 2073     2.0KB/s   00:00
kube-scheduler                                                                                                     100% 2360     2.3KB/s   00:00
kube-controller-manager                                                                                            100% 2672     2.6KB/s   00:00
kube-apiserver                                                                                                     100% 2358     2.3KB/s   00:00
reconfDocker.sh                                                                                                    100% 2068     2.0KB/s   00:00
etcd                                                                                                               100%   16MB  15.9MB/s   00:00
etcdctl                                                                                                            100%   14MB  13.7MB/s   00:00
kube-scheduler                                                                                                     100%   77MB  76.9MB/s   00:00
kube-controller-manager                                                                                            100%  134MB 134.4MB/s   00:01
flanneld                                                                                                           100%   16MB  15.8MB/s   00:00
kube-apiserver                                                                                                     100%  144MB 144.0MB/s   00:00
kubelet                                                                                                            100%  123MB 122.7MB/s   00:00
flanneld                                                                                                           100%   16MB  15.8MB/s   00:00
kube-proxy                                                                                                         100%   69MB  69.4MB/s   00:00
a
```

> This does not seem to be a proper installation. Does it ? It does not say "Cluster validation succeeded."
I shall try some of the verification steps nevertheless.

```bash
$ cd ubuntu/binaries/
amit@kube1:~/kubernetes/cluster/ubuntu/binaries$

amit@kube1:~/kubernetes/cluster/ubuntu/binaries$ kubectl get nodes
kubectl: command not found

amit@kube1:~/kubernetes/cluster/ubuntu/binaries$ ls -ltr
total 81280
drwxrwxr-x 2 amit amit     4096 Oct 19 12:48 master
drwxrwxr-x 2 amit amit     4096 Oct 19 12:48 minion
-rwxr-x--- 1 amit amit 83220624 Oct 19 12:48 kubectl

amit@kube1:~/kubernetes/cluster/ubuntu/binaries$ ./kubectl get nodes
The connection to the server localhost:8080 was refused - did you specify the right host or port?
amit@kube1:~/kubernetes/cluster/ubuntu/binaries$
```

> So things are not right. I quickly remembered the default docker bridge should be removed
if we want to use kubernetes networking. However there may be many such things. Does this
install script does not take care of these issues ? Let me check the troubleshooting sections
instead.

- I could not find kubernetes or etcd specific logs at /var/log/upstart/
- Lets try to shut down the kube cluster

```bash
$ KUBERNETES_PROVIDER=ubuntu ./kube-down.sh

Bringing down cluster using provider: ubuntu
Identity added: /home/amit/.ssh/id_rsa (/home/amit/.ssh/id_rsa)
The connection to the server 172.16.115.2:8080 was refused - did you specify the right host or port?
The connection to the server 172.16.115.2:8080 was refused - did you specify the right host or port?
The connection to the server 172.16.115.2:8080 was refused - did you specify the right host or port?
The connection to the server 172.16.115.2:8080 was refused - did you specify the right host or port?
./../cluster/../cluster/ubuntu/util.sh: line 696: kubectl: command not found
Cleaning on master 172.16.115.2
Connection to 172.16.115.2 closed.
Cleaning on master 172.16.115.2 failed
[sudo] password for amit:
Connection to 172.16.115.2 closed.
[sudo] password for amit:
Connection to 172.16.115.2 closed.
Cleaning on node 172.16.115.3
Connection to 172.16.115.3 closed.
Cleaning on node 172.16.115.3 failed
[sudo] password for amit:
Connection to 172.16.115.3 closed.
Cleaning on node 172.16.115.4
Connection to 172.16.115.4 closed.
Cleaning on node 172.16.115.4 failed
[sudo] password for amit:
Connection to 172.16.115.4 closed.
Done
amit@kube1:~/kubernetes/cluster$
```

- The services' settings present in the system are ?

```
$ ls -ltr /etc/default/
aufs           console-setup  dbus           grub           keyboard       ntfs-3g        rsync          ufw
bridge-utils   crda           devpts         halt           locale         ntpdate        rsyslog        useradd
bsdmainutils   cron           docker         irqbalance     nss            rcS            ssh
```

- I could see a kube folder creation & some files at ~/kube location

```
# pwd
/home/amit/kube
root@kube1:/home/amit/kube#

root@kube1:/home/amit/kube# ls -ltr
total 108
drwxrwxr-x 2 amit amit  4096 Oct 19 13:47 default
-rwxr-x--- 1 amit amit 29633 Oct 19 13:47 util.sh
-rwxr-x--- 1 amit amit  2068 Oct 19 13:47 reconfDocker.sh
drwxr-x--- 2 amit amit  4096 Oct 19 13:47 init_scripts
drwxr-x--- 2 amit amit  4096 Oct 19 13:47 init_conf
-rw-rw-r-- 1 amit amit 43444 Oct 19 13:47 easy-rsa.tar.gz
-rwxr-x--- 1 amit amit  5587 Oct 19 13:47 config-default.sh
drwxrwxr-x 2 amit amit  4096 Oct 19 13:47 master
drwxrwxr-x 2 amit amit  4096 Oct 19 13:47 minion
```

> Should i try elevating myself for the entire duration of running this kube script ?

```bash
amit@kube1:~/kubernetes/cluster$ sudo -i
[sudo] password for amit:
root@kube1:~#
root@kube1:~# pwd
/root

root@kube1:/home/amit/kubernetes/cluster# KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

... Starting cluster using provider: ubuntu
... calling verify-prereqs
Could not find or add an SSH identity.
Please start ssh-agent, add your identity, and retry.
```

> Oops, I face the earlier issue. I tried to generate key pairs for root user. Then shipped the
root user's public key to normaluser@hosts (hosts here mean master & 2 nodes).
However, it resulted in the last response where I don't get any success message. 

> Well, I tried to look into the source code at github. It tries to download the file 
saltbase/salt/generate-cert/make-ca-cert.sh which is not available in the releases
tar that I had downloaded. This is even clearly seen in the terminal.

> Lets try to do this once again, however doing a git clone of kubernetes repo.


```bash

git clone --depth 1 https://github.com/kubernetes/kubernetes.git kubenetes-git
```

- edit the cluster/ubuntu/config-default.sh file within the newly downloaded git repo
  - service - 192.16.1.0/24 - should not conflict with flannel
  - flannel - 182.16.0.0/16 - should be a private ip
  - dns server - 192.16.1.10

> Try again !!

```
$ KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

# it went past the last error.
[sudo] password to start master:
etcd start/running, process 3416
etcd cluster has no published client endpoints.
Try '--no-sync' if you want to access non-published client endpoints(http://127.0.0.1:4001,http://127.0.0.1:2379).
Error:  client: no endpoints available
etcd cluster has no published client endpoints.
Try '--no-sync' if you want to access non-published client endpoints(http://127.0.0.1:2379,http://127.0.0.1:4001).
Error:  client: no endpoints available
Error:  100: Key not found (/coreos.com) [12]
{"Network":"182.16.0.0/16", "Backend": {"Type": "vxlan"}}
{"Network":"182.16.0.0/16", "Backend": {"Type": "vxlan"}}
docker stop/waiting
docker start/running, process 3704
Connection to 172.16.115.2 closed.

Deploying node on machine 172.16.115.3
```

> Hmmn. Looks like so many errors are still there. Probably config related issues.
Let me see if some more service settings have been set now. 

```bash
$ ls -ltr /etc/default/
aufs                     dbus                     halt                     kube-proxy               rcS
bridge-utils             devpts                   irqbalance               kube-scheduler           rsync
bsdmainutils             docker                   keyboard                 locale                   rsyslog
console-setup            etcd                     kube-apiserver           nss                      ssh
crda                     flanneld                 kube-controller-manager  ntfs-3g                  ufw
cron                     grub                     kubelet                  ntpdate                  useradd
```

> Looks like kube related, etcd related services' settings have come up. Lets check if there 
were any network related changes ?

```bash

# at master

$ ip -4 l
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:1e:67:a0:0b:74 brd ff:ff:ff:ff:ff:ff
3: eth1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DEFAULT group default qlen 1000
    link/ether 00:1e:67:a0:0b:75 brd ff:ff:ff:ff:ff:ff
5: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN mode DEFAULT group default
    link/ether 96:b7:21:7a:24:a5 brd ff:ff:ff:ff:ff:ff
6: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:b7:5d:08:e4 brd ff:ff:ff:ff:ff:ff


$ ip -4 addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet 172.16.115.2/16 brd 172.16.255.255 scope global eth0
       valid_lft forever preferred_lft forever
5: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    inet 182.16.80.0/16 scope global flannel.1
       valid_lft forever preferred_lft forever
6: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    inet 182.16.80.1/24 scope global docker0
       valid_lft forever preferred_lft forever

$ ip -4 route
default via 172.16.1.1 dev eth0
172.16.0.0/16 dev eth0  proto kernel  scope link  src 172.16.115.2
182.16.0.0/16 dev flannel.1  proto kernel  scope link  src 182.16.80.0
182.16.80.0/24 dev docker0  proto kernel  scope link  src 182.16.80.1

# at one worker node
$ ip -4 route
default via 172.16.1.1 dev eth0
172.16.0.0/16 dev eth0  proto kernel  scope link  src 172.16.115.3
172.17.0.0/16 dev docker0  proto kernel  scope link  src 172.17.0.1

```

> So the master node has got these changes. The previous docker0 bridge settings have also changed.
However, there were no changes to worker nodes. Finally, lets check if kubectl got installed.

```bash
$ kubectl
kubectl: command not found
```

> Nopes. Well, lets go back to the latest errors.

```
etcd cluster has no published client endpoints.
Try '--no-sync' if you want to access non-published client endpoints(http://127.0.0.1:4001,http://127.0.0.1:2379).
Error:  client: no endpoints available
etcd cluster has no published client endpoints.
Try '--no-sync' if you want to access non-published client endpoints(http://127.0.0.1:2379,http://127.0.0.1:4001).
Error:  client: no endpoints available
Error:  100: Key not found (/coreos.com) [12]
```

> Lets check the flannel log to verify if this is a network issue ?


```bash
sudo cat /var/log/upstart/flanneld.log

[sudo] password for amit:
I1019 15:08:14.331949 03427 main.go:275] Installing signal handlers
I1019 15:08:14.335532 03427 main.go:188] Using 172.16.115.2 as external interface
I1019 15:08:14.335579 03427 main.go:189] Using 172.16.115.2 as external endpoint
E1019 15:08:14.427355 03427 network.go:53] Failed to retrieve network config: 100: Key not found (/coreos.com) [0]
E1019 15:08:15.427742 03427 network.go:53] Failed to retrieve network config: 100: Key not found (/coreos.com) [12]
E1019 15:08:16.428710 03427 network.go:53] Failed to retrieve network config: 100: Key not found (/coreos.com) [12]
E1019 15:08:17.429585 03427 network.go:53] Failed to retrieve network config: 100: Key not found (/coreos.com) [12]
I1019 15:08:18.503738 03427 etcd.go:204] Picking subnet in range 182.16.1.0 ... 182.16.255.0
I1019 15:08:18.504135 03427 etcd.go:84] Subnet lease acquired: 182.16.80.0/24
I1019 15:08:18.505305 03427 ipmasq.go:50] Adding iptables rule: FLANNEL -d 182.16.0.0/16 -j ACCEPT
I1019 15:08:18.508103 03427 ipmasq.go:50] Adding iptables rule: FLANNEL ! -d 224.0.0.0/4 -j MASQUERADE
I1019 15:08:18.509860 03427 ipmasq.go:50] Adding iptables rule: POSTROUTING -s 182.16.0.0/16 -j FLANNEL
I1019 15:08:18.511650 03427 ipmasq.go:50] Adding iptables rule: POSTROUTING ! -s 182.16.0.0/16 -d 182.16.0.0/16 -j MASQUERADE
I1019 15:08:18.513532 03427 vxlan.go:153] Watching for L3 misses
I1019 15:08:18.513553 03427 vxlan.go:159] Watching for new subnet leases
I1019 15:08:18.514154 03427 vxlan.go:273] Handling initial subnet events
I1019 15:08:18.514163 03427 device.go:159] calling GetL2List() dev.link.Index: 5
```

- **Meanwhile did you understand the iptables rules that was created above ?**

> Well, could not understand much. I shall go as per, the link
https://github.com/kubernetes/kubernetes/issues/17205 & will change the 
etcdctl setting at cluster/ubuntu/reconfDocker.sh & add `--no-sync`.

```bash
# modified the file at /cluster/ubuntu/reconfDocker.sh

$ KUBERNETES_PROVIDER=ubuntu ./kube-down.sh

```

> Hopefully, there are no further issues.

```bash
$ KUBERNETES_PROVIDER=ubuntu ./kube-up.sh

# usual messages ...

Validating master
Validating amit@172.16.115.2
Validating amit@172.16.115.3
Validating amit@172.16.115.4
Using master 172.16.115.2
cluster "ubuntu" set.
user "ubuntu" set.
context "ubuntu" set.
switched to context "ubuntu".
Wrote config for ubuntu to /home/amit/.kube/config
... calling validate-cluster
Found 3 node(s).
NAME           STATUS    AGE
172.16.115.2   Ready     30s
172.16.115.3   Ready     18s
172.16.115.4   Ready     7s
Validate output:
NAME                 STATUS    MESSAGE              ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-0               Healthy   {"health": "true"}
Cluster validation succeeded
Done, listing cluster services:

Kubernetes master is running at http://172.16.115.2:8080

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

> Wow, we have succeeded in creating a Kubernetes cluster of a Ubuntu master & 
two Ubuntu worker nodes.

```bash
amit@kube1:~/kubenetes-git/cluster/ubuntu/binaries$ ./kubectl get nodes
NAME           STATUS    AGE
172.16.115.2   Ready     5m
172.16.115.3   Ready     5m
172.16.115.4   Ready     5m

# at one of the worker nodes

$ ip -4 route
default via 172.16.1.1 dev eth0
172.16.0.0/16 dev eth0  proto kernel  scope link  src 172.16.115.3
182.16.0.0/16 dev flannel.1  proto kernel  scope link  src 182.16.16.0
182.16.16.0/24 dev docker0  proto kernel  scope link  src 182.16.16.1
```
