---
layout: post
title: KVM based crash consistent CloudByte storage snapshots - WIP
---

# KVM based crash consistent CloudByte storage snapshots - **WIP**

> This article tries to collage information on how to implement a perfect
> crash consistent snapshot for VMs running on KVM based hypervisors. This
> article focuses on KVM kernel module with Ubuntu as the host OS.

## Overall setup:

```
- OpenStack Liberty single node setup
- Ubuntu 14.04.1 as KVM based hypervisor (will host OpenStack)
- Libvirt 1.2.2 on hypervisor
- CentOS VM 7.0
- CloudByte storage volumes will be iSCSI based
```

## OpenStack devstack (version - Liberty) install verifications:

- Refer to below link for details:
  - [link](http://cloudbytestorages.github.io/blog/general/setup/openstack/2016/05/11/OpenStack-Configuration-With-CloudByte-Cinder-Driver.html)

```
# Assuming, the devstack liberty has been installed

> cd /home/cbos/devstack

> git status
On branch stable/liberty
Your branch is up-to-date with 'origin/stable/liberty'.

> cinder list
ERROR: You must provide a user name through --os-username or env[OS_USERNAME].

# The answer lies in understanding the openrc bash file.
# Find it under the devstack installed location.
# This file sources various bash files like functions, stackrc, etc.

# It also sources an environment file named ".stackenv".
# It has the necessary password & other services specific settings for devstack.

# need to set the username & tenant name via "openrc" bash file.
> source openrc admin admin

> cinder list
# will provide some list of volumes if already created
```

## OpenStack CloudByte cinder code refresh:

- This is required if we want the hot patches, fixes from CloudByte team.
- These may not be present in upstream OpenStack maintained repository.

```
# check for the presence of 3 files named:
# cloudbyte.py, options.py & __init__.py at below location

> cd /opt/stack/cinder/cinder/volume/drivers/cloudbyte

# Get the liberty cinder drier with latest hot fixes from below location:
# https://github.com/CloudByteStorages/openstack/tree/master/liberty/cloudbyte

# Once the 3 files are replaced, the cinder services needs to be restarted

> cd /home/cbos/devstack
> ./rejoin-stack.sh

# use Ctrl + a + p -> previous screen
# use Ctrl + a + n -> next screen
# use Ctrl + a + d -> detach i.e. exit

# multiple instances of cinder-api services leads to bind errors

RuntimeError: Could not bind to 0.0.0.0:8776 after trying for 30 seconds
ERROR cinder Traceback (most recent call last):
ERROR cinder   File "/usr/local/bin/cinder-api", line 10, in <module>
ERROR cinder     sys.exit(main())
ERROR cinder   File "/opt/stack/cinder/cinder/cmd/api.py", line 56, in main
ERROR cinder     server = service.WSGIService('osapi_volume')
ERROR cinder   File "/opt/stack/cinder/cinder/service.py", line 377, in __init__
ERROR cinder     port=self.port)
ERROR cinder   File "/opt/stack/cinder/cinder/wsgi/eventlet_server.py", line 176, 
                 in __init__
ERROR cinder     {'host': host, 'port': port})
ERROR cinder RuntimeError: Could not bind to 0.0.0.0:8776 after trying for 30 seconds

# verify the no of cinder services that are running in the devstack box
ps -aux | grep cinder | grep -v grep

# There are commonly 3 cinder services that will be running (there are more actually).
# Common ones are: cinder-scheduler, cinder-volume & cinder-api
# Kill all the cinder services if above bind error occurs
# kill -9 <pid>

# navigate to screen & start these services
> screen -x

# in c-api screen run below commmand to start cinder-api
/usr/local/bin/cinder-api --config-file /etc/cinder/cinder.conf

# in c-sch screen run below commmand to start cinder-scheduler
/usr/local/bin/cinder-scheduler --config-file /etc/cinder/cinder.conf

# in c-vol screen run below commmand to start cinder-volume
/usr/local/bin/cinder-volume --config-file /etc/cinder/cinder.conf

# verify the logs in each cinder screens

# detach from screen
> Ctrl + a + d
```

## OpenStack CloudByte cinder configuration:

- Refer below link for details:
  - [link](http://cloudbytestorages.github.io/blog/general/setup/openstack/2016/05/11/OpenStack-Configuration-With-CloudByte-Cinder-Driver.html)

```
# open the cinder.conf & verify the settings mentioned below.
# change the values of CloudByte specific settings based on your environment.
# CloudByte specific settings start with "cb_" as its prefix.

> vi /etc/cinder/cinder.conf

..
[DEFAULT]
default_volume_type = cb-gold
enabled_backends = cloudbyte-gold
iscsi_helper = tgtadm
..

[cloudbyte-gold]
volume_driver = cinder.volume.drivers.cloudbyte.cloudbyte.CloudByteISCSIDriver
volume_backend_name = cloudbyte-gold
san_ip = 20.10.43.1
san_login = admin
san_password = na
cb_tsm_name = OPENSTACK_VSM
cb_account_name = OPENSTACK_ACC
cb_apikey = gaxFrefF7h_-SUxVPODDYsvGxZwtn0jnMChUeElNcIcZzP1XNw8YGKhcvCO46AqJCXsHVePmaJcBNYydagREmw
cb_confirm_volume_create_retries = 15
cb_confirm_volume_create_retry_interval = 5
cb_confirm_volume_delete_retries = 15
cb_confirm_volume_delete_retry_interval = 5
cb_add_qosgroup = latency:15,graceallowed:true,iopscontrol:true,memlimit:0,tpcontrol:false,throughput:0,iops:20,networkspeed:0
cb_create_volume = compression:off,deduplication:off,blocklength:512B,sync:always,protocoltype:ISCSI,recordsize:4k
cb_initiator_group_name = ALL
..

```

## Verify KVM as the hypervisor in Ubuntu:

For details refer to below link:

- [OpenStack nova](http://docs.openstack.org/liberty/config-reference/content/kvm.html)
- [Ubuntu and KVM](https://help.ubuntu.com/community/KVM/Installation)

### Check virtualization support:

```
# check if CPU supports hardware virtualization
> egrep -c '(vmx|svm)' /proc/cpuinfo

# 0 means CPU does not support hardware virtualization.
# 1 or more means CPU supports

# check if virtualization is enabled in BIOS

# Alternatively, check the output of kvm-ok
> kvm-ok

# check if kvm kernel modules are loaded
# if the output has kvm_intel or kvm_amd, the kvm hardware virtualization
# modules are loaded
> lsmod | grep kvm

# It is better to use a 64-bit kernel
# else the VMs that get spawned are limited to 2 GB RAM.

# Run below command to identify if the processor is 64-bit
> egrep -c ' lm ' /proc/cpuinfo

# if value is 1 or more, then it is a 64-bit CPU

# Check the kernel mode
> uname -m

# x86_64 indicates a 64-bit kernel.
# NOTE - i386, i486, i586 or i686 are 32-bit kernel.
```

### Various packages to manage VMs:

```
# Some of below are must & some are good to have::

# libvirt-bin provides libvirtd (to administer qemu & kvm instances)
# qemu-kvm is the backend
# ubuntu-vm-builder (cli to build VMs)
# bridge-utils (provides bridge from hypervisor network to VMs)
# virt-viewer to view instances
```

### Libvirt verifications:

```
# check the installation
> libvirtd
2016-07-01 09:00:17.331+0000: 31430: info : libvirt version: 1.2.2
...

# check if /var/log/libvirt/libvirtd.log has recent errors ?

# Check if below command results in an error, failure to connect, etc.?
> virsh -c qemu:///system list
Id    Name                           State
----------------------------------------------------
2     instance-00000006              running

# check write access to libvirt-sock file
# below is a proper access
> ls -la /var/run/libvirt/libvirt-sock
srwxrwx--- 1 root libvirtd 0 Jun 30 16:39 /var/run/libvirt/libvirt-sock

# check if /dev/kvm needs to be in the right group
> # ls -l /dev/kvm
crw-rw----+ 1 root kvm 10, 232 Jun 30 16:39 /dev/kvm
```

### Check OpenStack settings:

```
# verify virt_type property in /etc/nova/nova.conf file
> vi /etc/nova/nova.conf

...
[libvirt]
virt_type = kvm
...
```
