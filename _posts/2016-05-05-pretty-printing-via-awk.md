---
layout: post
title: Pretty printing via awk
---

Pretty printing via awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# refer : http://backreference.org/2010/02/10/idiomatic-awk/
# the entire credit goes to above links & their authors
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# Pretty printing sequences
seq 1 30
# 1
# 2
..
# 30

# ORS - Output Record Separator is usually '\n'
# NR - refers to no of records in the file / std output
# FS - Field Seprator is usually whitespace
# RS - Record Separator which is same as '\n'
seq 1 30 | awk 'ORS = NR % 5 ? FS : RS'
# 1 2 3 4 5
# 6 7 8 9 10
..
# 26 27 28 29 30
```
