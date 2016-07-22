---
layout: post
title: Changing delimiters using awk
---

Changing delimiters using awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows its power
# the power of functional awk vs. all those imperative languages.

# via awk cli change the default Field Separator
# via awk cli change the default Output Field Separator
# $1=$1 forces recomputation of $0
# 1 represents an always true condition in second sequence
# Absence of action in second sequence defaults to print $0
awk -v FS=';' -v OFS=',' '{$1=$1}1'
```
