---
layout: post
title: Performance complexity and memory usage in awk
---

Performance complexity and memory usage in awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# Finding unique lines of a file
# NOTE - Read below article to understand a bit about complexity & memory usage
# refer - http://unix.stackexchange.com/questions/128775/
# When awk reads a line, it does a hash lookup to verify if line is in memory
# It stores the unique lines as hash keys & a counter as the value
awk '!x[$0]++' file.txt

# NOTE - sort resorts to temporary files to avoid filling up the memory
# This is better than letting the system swapping memory randomly to disk.
# This swapping impacts other running applications on the system.
# If awk runs out of memory then sort is the solution with longer times.

# NOTE - Issues with hashing which is supposedly O(1) operation.
# refer - http://unix.stackexchange.com/questions/225962/
# Issues might be with the collisions, & handling of collisions.
# i.e. huge data is not spread, & is not filling the buckets.
# In addition, awk is interpreted.
```
