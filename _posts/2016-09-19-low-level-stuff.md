---
layout: post
title: Low level stuff
---

## Low level stuff

### TCMU - TCM Userspace Design

refer - [tcmu design](https://www.kernel.org/doc/Documentation/target/tcmu-design.txt)
refer - target_core_user

- TCM is another name for LIO
- LIO an **in-kernel** iSCSI target
- TCMU implies TCM in Userspace
- TCMU allows userspace programs to be written that act as iSCSI targets.
- TCM modularizes data storages into modules.
- TCM data storage modules are called as backstores or storage engines
  - Some of these backstores are file, block device, RAM or using another SCSI device
  - These are implemented entirely as kernel code

<br />

- NOTE - Similar to LIO is tgt.
- tgt is a userspace iSCSI target solution.
- tgt acts as a translator to non-traditional networked storage systems
- NOTE - Non-traditional implies Gluster's GLFS or Ceph's RBD
- tgt makes it easy to support any non-traditional storages via use of adapter modules
- tgt adapter will use the available userspace libs for RBD, GLFS etc.

<br />

- How to implement similar userspace storage adapters for LIO which is kernel space ?
- Do we need to port the non-traditional file system APIs o kernel space ?
- No. We need to create a userspace pass-through backstore for LIO i.e. TCMU

<br />

- Why LIO ?
- TCMU combines with the LIO loopback fabric & becomes something similar to FUSE
- However this is at SCSI layer & not at file system layer.
- TCMU design
  - A memory region is shared between kernel & userspace
  - This region has a control area i.e. a mailbox
  - This region has a lockless producer/consumer circular buffer for commands & status
  - This region has an in/out data buffer area

<br />

- TCMU's use of UIO
  - UIO allows device driver development in userspace
  - However, TCMU does it for SCSI commands
  - UIO handles the device introspection part
    - i.e. userspace can determine how large the shared region is
  - UIO is useful as a signaling mechanism in both directions
 
<br />

### loop device

- refer [link](https://en.wikipedia.org/wiki/Loop_device)
- makes a file accessible as a block device
- hence, must be connected to an existing file as a filesystem
- hence, if the file is an entire file system, the file needs to be **mounted** as if it were a **disk device**
 - loop mounting makes such files accessible
- TIP - it is incorrectly referred to as loop-back devices

<br />

### mounting a file containing a disk image on a directory

- Run a command to let content of the file **used as a file system** rooted at a given mount point

```shell

# identify an available loop device
losetup -f

# my.img is a regular file containing a filesystem
# /home/damit/play is a directory of user damit

losetup /dev/loop0 my.img
mount /dev/loop1 /home/damit/play

# oneliner to do above steps
mount -o loop my.img /home/damit/play

# unmounting
unmount home/damit/play
# or, mount | grep "/home/damit/play"
# or, losetup -a | grep my.img
# you get the exact loop device
unmount /dev/loop<<N>>

```

- At a lower level, the assoc & de-assoc of a file with a loop is done via ioctl system call on the loop device

### Configfs

- Is a RAM based virtual filesystem provided by 2.6 Linux kernel
- Configfs appears similar to sysfs but they are in fact different and complementary. 
- Configfs is for creating, managing and destroying kernel objects from user-space, 
- Sysfs for viewing and manipulating objects from user-space which are created and destroyed by kernel space.
- Configfs is typically mounted at /sys/kernel/config (or more rarely at /config).
