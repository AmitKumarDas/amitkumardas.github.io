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

## Experiences with Rancher

- Running Rancher is as simple as launching two containers. 
- One container as the management server and another container on a node as an agent. 
- Launching Management Server: docker run -d --restart=always -p 8080:8080 rancher/server
 - The UI and API are available on the exposed port 8080.

<br />

### Hosts in Rancher OS

- Host gets registered & connected to Rancher Server
 - This happens when rancher agent is started on the host
 - The registration token is used by the rancher agent to connect to the Rancher Server for the first time
 - Agent is untrusted as it is running on the outside & potentially hostile to Rancher Server
 - Agent accounts have access to only the resources they need in the API
 - IPSec key is per environment. It is generated on the server, stored in the database
  - It is sent to the host as part of the agent registration with the API key pair.

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

