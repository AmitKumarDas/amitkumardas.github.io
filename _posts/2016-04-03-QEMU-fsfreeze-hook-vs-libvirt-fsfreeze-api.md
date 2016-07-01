---
layout: post
title: QEMU's fsfreeze hook vs. libvirt's fsfreeze API
---

QEMU's fsfreeze hook vs. libvirt's fsfreeze API

- A description of QEMU agent's hook has been succintly mentioned in the source itself:
  - [link](https://raw.githubusercontent.com/qemu/qemu/master/scripts/qemu-guest-agent/fsfreeze-hook)


```
- In short, qemu agent hook enables scripting at below phases:
  - pre-freeze &
  - post-thaw
- The hook script is only applicable for IO specific application running inside any VM.
- Storage on the other hand would want to take snapshots during below phases:
  - post-freeze or
  - pre-thaw.
```


- Hence, storage infrastructure would seek the help of libvirt API for above feasibility.
  - Details available at [OpenStack link 1](https://review.openstack.org/#/c/72038/) &
  - [OpenStack link 2](https://wiki.openstack.org/wiki/Cinder/QuiescedSnapshotWithQemuGuestAgent)

> To summarize, when qemu-guest-agent is installed in a kvm instance, we can request the
> instance to fsfreeze via libvirt's fsFreeze API which is introduced in **libvirt 1.2.5**.


- Required libvirt commands:
  - guest-fsfreeze-freeze
  - guest-fsfreeze-thaw
