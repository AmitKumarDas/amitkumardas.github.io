---
layout: post
title: My Low Level Troubleshooting Stories
---

## My Low Level Troubleshooting Stories

### Address already in use

```bash

Error (500 Server Error: Internal Server Error ("driver failed programming external connectivity on endpoint 
r-wekan_wekan_1 (d498ed937dbd3490b05c5e89251fa1073071412cb0e2a4e5606e3cf16e48bfc6): Error starting userland 
proxy: listen tcp 0.0.0.0:80: bind: address already in use")
```

<br />

```bash
# netstat with grep will not help

netstat -tp | grep 80
tcp        0      0 localhost:48007         localhost:44085         ESTABLISHED 9755/python
tcp        0      0 localhost:44085         localhost:48007         ESTABLISHED 9755/python

# use lsof
lsof -i :80
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2  2424     root    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
apache2 24790 www-data    4u  IPv6  16600      0t0  TCP *:http (LISTEN)
apache2 24791 www-data    4u  IPv6  16600      0t0  TCP *:http (LISTEN)

# more precise command
lsof -i tcp:80

# Alternative: use fuser
fuser 80/tcp
Cannot stat file /proc/26794/fd/4: Stale file handle
Cannot stat file /proc/26794/fd/5: Stale file handle
Cannot stat file /proc/26794/fd/6: Stale file handle
Cannot stat file /proc/26794/fd/7: Stale file handle
Cannot stat file /proc/26794/fd/11: Stale file handle
80/tcp:               2424 24790 24791
```

<br />

- lsof: list of open files
  - regular file, a directory, a block special file, a character special file, 
  - a library, 
  - a stream or a network file (Internet socket, NFS file, or UNIX domain socket)\
- has options to exclude kernel blocking paths

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

<br />

### Linux Server Security - Hardening

- refer [link](https://dzone.com/articles/9-useful-tips-for-linux-server-security?edition=155252)
