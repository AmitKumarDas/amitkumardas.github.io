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

### Check OpenStack KVM settings:

```
# verify virt_type property in /etc/nova/nova.conf file
> vi /etc/nova/nova.conf

...
[libvirt]
virt_type = kvm
...
```

<br />

### Upgrade libvirt to have freeze & thaw support

- [Upgrade libvirt on Ubuntu](http://nolimitsdesigns.com/game-design/ubuntu-14-04-libvirt-compile-and-install/)
- __or__
- [Use Ubuntu 16.04](https://wiki.openstack.org/wiki/LibvirtDistroSupportMatrix)
- Try various scenarios & check if OpenStack responds properly to libvirt upgrade.

```
# NOTE - I had upgrade the libvirt when logged in as 'root' user !!
# NOTE - devstack was installed under user 'cbos' !!

# Check if all the nova services are running or not.
> nova service-list
+----+------------------+----------+-------+------------------------------------+
| Id | Binary           | Status   | State | Disabled Reason                    |
+----+------------------+----------+-------+------------------------------------+
| 1  | nova-conductor   | enabled  | up    | -                                  |
| 3  | nova-cert        | enabled  | up    | -                                  |
| 4  | nova-network     | enabled  | up    | -                                  |
| 5  | nova-scheduler   | enabled  | up    | -                                  |
| 6  | nova-consoleauth | enabled  | up    | -                                  |
| 7  | nova-compute     | disabled | up    | AUTO: Failed to connect to libvirt |
+----+------------------+----------+-------+------------------------------------+

# In my case the compute service was disabled with below reason:
# AUTO: Failed to connect to libvirt

# run nova hypervisor-list
>  nova hypervisor-list
+----+---------------------+-------+----------+
| ID | Hypervisor hostname | State | Status   |
+----+---------------------+-------+----------+
| 1  | cbos                | up    | disabled |
+----+---------------------+-------+----------+

# The permissions for libvirt-sock was as follows:
> ls -ltr /var/run/libvirt/libvirt-sock
srwxrwxrwx 1 root root 0 Jul  4 10:49 /var/run/libvirt/libvirt-sock

# under root, virsh list command worked fine
# this was to check if errors with libvirt itself
> virsh -c 'qemu:///system' list
 Id    Name                           State
----------------------------------------------------

# restart the compute service
> sg libvirtd '/usr/local/bin/nova-compute --config-file /etc/nova/nova.conf'

# checked the service list
> nova service-list
+----+------------------+------+----------+---------+-------+-----------------+
| Id | Binary           | Host | Zone     | Status  | State | Disabled Reason |
+----+------------------+------+----------+---------+-------+-----------------+
| 1  | nova-conductor   | cbos | internal | enabled | up    | -               |
| 3  | nova-cert        | cbos | internal | enabled | up    | -               |
| 4  | nova-network     | cbos | internal | enabled | up    | -               |
| 5  | nova-scheduler   | cbos | internal | enabled | up    | -               |
| 6  | nova-consoleauth | cbos | internal | enabled | up    | -               |
| 7  | nova-compute     | cbos | nova     | enabled | up    | -               |
+----+------------------+------+----------+---------+-------+-----------------+

# Tried to create a VM from OpenStack
# It failed with below error:
# Error: No valid host was found. There are not enough hosts available.

# Checking the nova service list gave the original error
# i.e. nova-compute's status was disabled

# login to the terminal as root user gave below ouputs:

> cat /etc/group | grep cbos
adm:x:4:syslog,cbos
cdrom:x:24:cbos
sudo:x:27:cbos
dip:x:30:cbos
plugdev:x:46:cbos
lpadmin:x:108:cbos
cbos:x:1000:
sambashare:x:124:cbos
libvirtd:x:129:cbos

> groups
root

# NOTE - At this point, I logged-in to the terminal as cbos
# and started to run various commands as follows:

> users
cbos

> groups
cbos adm cdrom sudo dip plugdev lpadmin sambashare libvirtd

> libvirtd
info : libvirt version: 1.2.5
14890: error : virPidFileAcquirePath:414 : 
	Failed to acquire pid file '/run/user/1000/libvirt/libvirtd.pid': 
	Resource temporarily unavailable

> virsh --version
1.2.5

# running libvirtd multiple times would hang

# joining the devstack screens & running below command at n-cpu screen
> screen -x
sg libvirtd '/usr/local/bin/nova-compute --config-file /etc/nova/nova.conf'

# following errors were noticed
..
Connection event '0' reason 'Failed to connect to libvirt'
..
libvirtError: authentication failed: polkit: polkit\56retains_authorization_after_challenge=1
..
Authorization requires authentication but no agent is available.
..
HypervisorUnavailable: Connection to the hypervisor is broken on host: cbos

# running the virsh commands gave below results:
> virsh list
 Id    Name                           State
----------------------------------------------------

> virsh -c 'qemu:///system' list
error: failed to connect to the hypervisor
error: authentication failed: polkit: polkit\56retains_authorization_after_challenge=1
Authorization requires authentication but no agent is available.

# Modified /etc/libvirt/libvirtd.conf file
# Uncommented the unix_sock_group & set it as libvirtd
# & then ran below commands:

> sudo service libvirt-bin restart
libvirt-bin stop/waiting
libvirt-bin start/running, process 16142

> sudo libvirtd
2016-07-04 07:03:10.456+0000: 16233: info : libvirt version: 1.2.5
2016-07-04 07:03:10.456+0000: 16233: error : virPidFileAcquirePath:414 : Failed to acquire pid file '/var/run/libvirtd.pid': Resource temporarily unavailable

> virsh -c 'qemu:///system' list
error: failed to connect to the hypervisor
error: authentication failed: polkit: polkit\56retains_authorization_after_challenge=1
Authorization requires authentication but no agent is available.

> sudo virsh -c 'qemu:///system' list
 Id    Name                           State
----------------------------------------------------

> ls -ltr /var/run/libvirt/libvirt-sock
srwxrwxrwx 1 root libvirtd 0 Jul  4 12:33 /var/run/libvirt/libvirt-sock

# logging out & relogin using cbos
# & then restarting the service at n-cpu did not help
# it gave the same errors.

# Finally, did a devstack unstack & stack.sh as above did not solve the issue.

```
