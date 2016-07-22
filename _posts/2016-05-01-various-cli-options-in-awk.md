---
layout: post
title: Various cli options in awk
---

Various cli options in awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# refer : http://backreference.org/2010/02/10/idiomatic-awk/
# the entire credit goes to above links & their authors
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# NOTE - FS i.e. custom field separator
echo 20.10 | awk -F. '{print $1 " " $2}'
# 20 10

# NOTE - Alternate way towards custom field separator
echo 20.10 | awk -F '[.]' '{print $1 " " $2}'
# 20 10
```
