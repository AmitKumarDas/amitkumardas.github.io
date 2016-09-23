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

### Docker Image

- Setup details are in a file called Dockerfile
- File is placed in the project root location
- Dockerfile will be used to build a docker image
- Map the project directory to a directory (e.g. /code) in image
- Execute the project's dependencies command
 - e.g. RUN pip install -r requirements.txt
- Set the default command for the container
 - e.g. provide the command to start the application

<br />

### Building the docker image - Dockerfile

```

# assumption - Dockerfile file is present inside the project folder
# this will create a image called web

docker build -t web .
```

<br />

### Defining docker services - docker-compose

- This marks the use of **docker-compose.yml**
 - The **default path** of the compose file is ./docker-compose.yml
- This file will make use of docker images
- This file can also set to trigger building of image via the Dockerfile
- In other words you can compose an application made out of different images

### Advanced compose sections

- Modifying the code inside the container without rebuiliding the image is possible
- Compose version 1:
 - does not support volumes or networks
 - does not support build & image together
- Compose version 2: 
 - If image & build are present in the compose file, 
 - then the image is built with the value of the image
 ```
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
 ```shell
 
  ports:
   - "${EXTERNAL_PORT}:5000"
 ```

<br />

### Build & run your app - docker-compose

```
# in the project directory that has docker-compose.yml
# run below command

docker-compose up

# detached mode
# to run your services in the background

docker-compose up -d

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

