---
layout: post
title: Compare files without sort using awk
---

Compare files without sort using awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# NOTE - Checking if 2 similar files contain the same data or have differences
# First check the condition of presence of a line in array
# If condition fails, then insert the line into the array
# At the end, check if no of records in array w.r.t NR/2
# If program returns 0 then perfect match
# This program is a linear time operation.
awk '!($0 in a) {c++;a[$0]} END {exit(c==NR/2?0:1)}' file1 file2

```
