---
layout: post
title: My Troubleshooting Stories
---

## My Troubleshooting Stories

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
