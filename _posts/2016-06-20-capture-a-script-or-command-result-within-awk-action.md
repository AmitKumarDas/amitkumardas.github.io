---

layout: post

title: Capture a script or command's result within awk's action
---

How to capture a script's or a command's result within awk's action ?

```bash
#!/bin/bash
# refer:
# http://www.unix.com/shell-programming-and-scripting/73459-awk-system-getline.html

# getline enables this.
# NOTE - getline will get only the first line of the stdout
# NOTE - check how the command is defined, then executed, then closed !!!

echo 40 | awk ' { comm="test.sh "$1; comm | getline y; close(comm); print y } '
```
