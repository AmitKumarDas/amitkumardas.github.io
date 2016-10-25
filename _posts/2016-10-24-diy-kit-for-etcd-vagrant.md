---
layout: post
title: DIY kit for etcd - Vagrant
---

# DIY kit for etcd - Vagrant !!

Often I find requesting my admin to provide me a handful of nodes (some call it boxes) 
to install `xyz` tool that is meant to solve some distributed problem. Well we have AWS 
and so many other infrastructure providers to our rescue. However, their simplicity takes 
away the meat-ier concepts (*i.e. most valuable learning stuff*) with them. Eventually I 
get used to these simplifying stuff and become more & more dumb. Will it not be great to 
implement these problems in a series of `do it yourself from scratch`, get to the bottom 
of the problem and be a force of reason on the derived solution. 

If this sounds good, then lets start the journey of DIY kit for etcd.

I shall attempt to derive some reasonable understandings for queries on etcd. Questions on
etcd clustering, how etcd works, how to use etcd, etc. in these series of chapters.

<br />

## This is the 1st chapter in the series

We will start our learning journey by selecting a tool. The irony is `in-order to understand
a tool / library (`here etcd`) we need to grab may other tool as part of the process`. Isn't
this true in real life as well ? We invest on lots of concepts (*building blocks*) to 
understand a particular concept (*goal*). 

<br />

## etcd clustering via Vagrant

We shall try to understand the etcd clustering by installing a tool called Vagrant.

The immediate questions that comes to our mind here is:
- Why do you want to get into `clustering` ? 
- Why can you not settle with a single node etcd ?

Answer is "we can". However, etcd is meant for distributed architectures. Often, in my 
experience, we learn a lot by getting to do the things in a harder way. We become smart 
from our experiences. (*We can also be smart by understanding from others' experiences. 
However, do we really understand from others' experiences ?*)

Coming back, we were talking about Vagrant. Its an open source tool to build virtual environments. 
People use it to build virtual environments on thier laptops.  By default Vagrant uses virtualbox
for managing virtualization. *Did we just say a new tool ?* Yes, the name of the tool is 'virtualbox'.
You can think it as a contemporary to VMWare's ESX etc (*This may not be an apple to apple comparison,
but it is not relevant to our current journey*).

> Tip - vboxdrv, vboxnetft & vboxnetadp are VirtualBox host kernel modules. Vagrant's dkms package
ensures that these host kernel modules are properly updated if the Linux kernel version changes
during the next upgrade.

Typical steps to use vagrant for provisioning VMs are:

- add a box of some image with some specified name (*kind of nickname, etc*)
- configure a project directory & initialize a vagrant file within it
- set the vagrant file with the box name (the one that was added in first step)
- run `vagrant up` command within the project directory
- Once these are done, just run `vagrant ssh`

> Tip - Understand the difference between `vagrant up` & `vagrant reload`.

> Tip - Box is the act of building VM quickly. It will use a base image to clone a VM which in turn 
is supposedly faster than building a VM from scratch. These packaged base images are known as `boxes`.

> Tip - Once we are done, we can use `vagrant destroy`. NOTE - This does not remove the box file.

> Tip - Learn more on synced folders w.r.t Vagrant. /vagrant folder inside the guest is auto synced 
with the project directory in the host.

> Tip - /home/vagrant is different from /vagrant inside the guest.

<br />

## Automated Provisioning

Will above features of Vagrant enable us for creating repeatable images ?
The answer is yes, however it is limited. The concepts that we have learned is good to produce
repeatable OS images. What about the service (*i.e. applications*) we want to run ?

Vagrant provides options for just the above requirements. Create a shell file i.e. a bash file & 
write the commands that would install the software on top of the box image. This file will then be
referred from vagrant init file.

> Tip - Get a hang of Vagrant provisioning w.r.t reload option.

> Tip - Advanced packaging that binds the custom software with the box images can also be done.

<br />

## Networking

We will definitely want our applications running within the VM to be accessible from our host & also from other client machines. What are the options provided by Vagrant to do these ?

Vagrant will make use of a technique called `port forwarding`. Port forwarding enables us to specify ports on the guest machine that can be shared via a port on the host machine. Hence, this allows access to
the port on the host (*internally the network traffic will be forwarded to the specific port on the guest*).

> Tip - Port forwarding is definitely not the only way to do networking. There are other forms of networking as well e.g. assigning `static IP`, `bridge networking`, etc.

> Tip - Host only networking. Assigning a VM to a host-only network IP, allows you to access it
via the IP. Host-only networks can talk to the host machine as well as
any other machines on the same network, but cannot be accessed (through this
network interface) by any external networks.

> config.vm.network :hostonly, "192.168.33.10"

<br />

## References 

- https://www.olindata.com/blog/2014/07/installing-vagrant-and-virtual-box-ubuntu-1404-lts
- https://www.vagrantup.com/docs/getting-started/
