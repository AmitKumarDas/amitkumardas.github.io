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

<br />

## OpenStack devstack install verifications:

- Refer to below link for details:
  - [installation](http://cloudbytestorages.github.io/blog/general/setup/openstack/2016/05/11/OpenStack-Configuration-With-CloudByte-Cinder-Driver.html)
  - [verification](https://amitkumardas.github.io/2016/03/01/openstack-devstack-install-verifications.html)

<br />

## OpenStack CloudByte cinder code refresh:

- Refer to below link:
  - [CloudByte cinder driver code refresh](https://amitkumardas.github.io/2016/03/02/openstack-cloudbyte-cinder-code-refresh.html)

<br />

## OpenStack CloudByte cinder configuration:

- Refer below link for details:  
  - [driver config instructions](https://amitkumardas.github.io/2016/03/03/openstack-cloudbyte-cinder-driver-configuration.html)

<br />

## Verify KVM as the hypervisor in Ubuntu:

- For details refer to below link:
  - [OpenStack nova](http://docs.openstack.org/liberty/config-reference/content/kvm.html)
  - [Ubuntu and KVM](https://help.ubuntu.com/community/KVM/Installation)

<br />

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

<br />

### Various packages to manage VMs:

```
# Some of below are must & some are good to have::

# libvirt-bin provides libvirtd (to administer qemu & kvm instances)
# qemu-kvm is the backend
# ubuntu-vm-builder (cli to build VMs)
# bridge-utils (provides bridge from hypervisor network to VMs)
# virt-viewer to view instances
```

<br />

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

<br />

### Upgrade libvirt to have freeze & thaw support

- [Upgrade libvirt on Ubuntu](http://nolimitsdesigns.com/game-design/ubuntu-14-04-libvirt-compile-and-install/)
- __or__
- [Use Ubuntu 16.04](https://wiki.openstack.org/wiki/LibvirtDistroSupportMatrix)

<br />

### Check OpenStack KVM settings:

```
# verify virt_type property in /etc/nova/nova.conf file
> vi /etc/nova/nova.conf

...
[libvirt]
virt_type = kvm
...
```
