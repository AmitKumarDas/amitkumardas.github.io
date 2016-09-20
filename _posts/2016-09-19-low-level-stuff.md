---
layout: post
title: Low level stuff
---

## Low level stuff

### TCMU - TCM Userspace Design

refer - [tcmu design](https://www.kernel.org/doc/Documentation/target/tcmu-design.txt)

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
 
