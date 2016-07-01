---
layout: post
title: What is QEMU guest agent
---

What is QEMU guest agent ?

- It has been aptly described in this blog:
  - [blog link](https://bitcalm.com/blog/backup-of-virtual-machines-by-hypervisor-qemu-kvm/)

> QEMU guest agent is a utility which accepts commands from host by vireo-channel
> named org.qemu.guest_agent.0 and executes them as a guest. On the hypervisor's
> side channel ends with unix-socket where you can write text commands by **socat**
> utility. Libvirt uses this channel, to communicate with guest by
> **virsh <<qemu-agent-command>>**

- Possible QEMU guest agent commands:
  - refer to [link](https://bitcalm.com/blog/backup-of-virtual-machines-by-hypervisor-qemu-kvm/)
