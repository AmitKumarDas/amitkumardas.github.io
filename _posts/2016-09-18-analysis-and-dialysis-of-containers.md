---
layout: post
title: Analysis & Dialysis of Containers
---

## Analysis & Dialysis of Containers

<br />

### Install docker on Ubuntu

[refer install guide](https://docs.docker.com/engine/installation/linux/ubuntulinux/)

<br />

### UFW (Uncomplicated Firewall) forwarding & Docker

- docker uses a bridge to manage container networking
- by default, UFW drops all forwarding traffic & denies all incoming traffic
- If we want to reach a container from another host i.e. allow incoming connections on the Docker port
- Well you need to change the settings for these. Read on internet.

<br />

### Configure a DNS server to be used by Docker

- typically nameserver is set to 127.0.0.1 @ /etc/resolv.conf
- when docker containers are started on desktop machines, warnings are shown w.r.t DNS resolvers
- if you want to set a DNS server to the docker you need to change the settings. Refer internet.

<br />

### Configure docker to start on boot

- Ubuntu uses systemd as its boot & service manager from 15.04 onwards
- Above uses upstart for versions 14.10 & below
- to start docker on boot
 - sudo systemctl enable docker

<br />

### Basic docker related commands

```bash

# check service status
sudo service docker status

docker --version

# to install the latest version of docker
sudo apt-get upgrade docker-engine

# uninstall docker
sudo apt-get purge docker-engine

# uninstall docker & dependencies 
sudo apt-get autoremove --purge docker-engine

# delete the images, containers & volumes
rm -rf /var/lib/docker

# test docker installation
sudo docker run hello-world

# launch rancher server
sudo docker run -d --restart=always -p 8080:8080 rancher/server
```

<br />

### docker & networks

- to run a container on a particular network use --network option
  - docker run --network=<NETWORK>
- you can create your own user defined networks as well
- you can attach to a running container & investigate its network

```
$ docker attach container1

root@adhshd:/ # ifconfig

# ping to some other container from here

# CTRL-p CTRL-q to detach & leave it running
```

<br />

#### 3 networks are automatically created on installation of Docker

```bash
# docker network ls

NETWORK ID          NAME                DRIVER              SCOPE
d975c4b0d0a7        bridge              bridge              local
0ddc26e426ad        host                host                local
12c74021a20f        none                null                local

# ifconfig
docker0   Link encap:Ethernet  HWaddr 02:42:e8:f3:84:2f
          inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
          inet6 addr: fe80::42:e8ff:fef3:842f/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:55857 errors:0 dropped:0 overruns:0 frame:0
          TX packets:60148 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:39108722 (39.1 MB)  TX bytes:38049611 (38.0 MB)
```

<br />

#### docker & bridge network

- bridge network is the default one that docker daemon connects containers with
- the name of this bridge network is docker0
- ```ifconfig | grep docker0```
- bridge is part of a host's network stack
- ```docker network inspect bridge```
- docker engine creates a subnet & gateway to the bridge network
- containers in this default bridge network are able to communicate with each other using IP
- there is no automatic service discovery on this network
- allows the use of port mapping
- docker run --link allows communication between containers in the docker0 network
- NOTE - Docker advices this to be cumbersome & recommends to use custom defined networks

<br />

#### custom defined bridge network using docker

- ```docker network create --driver bridge my_br_0```
- ```docker network inspect my_br_0```
- ```docker network ls```
- ```docker run --netowkr=my_br_0 -itd --name=mycontainer busybox```
- ```docker network inspect my_br_0```


<br />

### Advanced docker-compose sections

- Modifying the code inside the container without rebuiliding the image is possible
- Compose version 1:
 - does not support volumes or networks
 - does not support build & image together
- Compose version 2: 
 - If image & build are present in the compose file, 
 - then the image is built with the value of the image
 
 ```yaml
  build: ./dir
  image: coolapp:1.2.3
 ```
 
 - ARG
  - These are specified in the Dockerfile
  - Then they are mentioned in the ```build``` tag in docker-compose.yml file
  - The values can be omitted in which case the value will extracted from the env
 ```yaml
  build:
   context: .
   args:
    - buildno
    - password
 ```
 
 - Variable substitution in compose file
 ```yaml
  ports:
   - "${EXTERNAL_PORT}:5000"
 ```
 
 - Container capabilities
 ```yaml
  cap_drop:
   - NET_ADMIN
   - SYS_ADMIN
 ```
 
 - cgroup_parent
  - can specify an optional parent cgroup for the container
 - container_name - be careful
  - scaling a container beyond 1 is not possible if it has been assigned a name
  - coz. a container must have a unique name

<br />

### Build & run your app - docker-compose

```
# in the project directory that has docker-compose.yml
# run below command

docker-compose up

# detached mode
# to run your services in the background

docker-compose up -d

# Aliter - try using ctrl-p and ctrl-q to detach from a container

# list the services
docker-compose ps

# Run one-off commands for your services
# here web is our service
# env will get the list of env variables available to web

docker-compose run web env

# want to learn on other available commands

docker-compose --help

# to stop your services

docker-compose stop
```

<br />

## Experiences with Rancher

- Running Rancher is as simple as launching **two** containers. 
- One container as the **management server** and another container on a node as **an agent**.
- Launching Management Server: 
 - ```docker run -d --restart=always -p 8080:8080 rancher/server```
 - The UI and API are available on the exposed port 8080.

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

### Hosts in Rancher OS

- Host gets registered & connected to Rancher Server
 - This happens when rancher agent is started on the host
 - The registration token is used by the rancher agent to connect to the Rancher Server for the first time
 - Agent is untrusted as it is running on the outside & potentially hostile to Rancher Server
 - Agent accounts have access to only the resources they need in the API
 - IPSec key is per environment. It is generated on the server, stored in the database
  - It is sent to the host as part of the agent registration with the API key pair.

<br />

### Services in Rancher OS

- A group of containers created from same docker image
- Leverages Rancher's DNS service for service discovery
- Can be deployed individually or by deploying an item from the Catalog
- Some of Rancher's builtin services:
 - load balancers
 - health monitoring
 - upgrade support
 - high-availability
- A sample service can be e.g. Trello
 - A Trello service may consist of 2 images
 - i.e. Trello App (3 containers) & Trello DB (1 container)

<br />

### Tips on Rancher

- Managed network: Can only "talk to" containers that are in the same service or linked to that service.
- Managed network: is on top of bridged. You get both IPs.
