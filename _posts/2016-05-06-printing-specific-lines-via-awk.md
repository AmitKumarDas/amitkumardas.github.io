---
layout: post
title: Printing specific lines via awk
---

Printing specific lines via awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# refer : http://backreference.org/2010/02/10/idiomatic-awk/
# the entire credit goes to above links & their authors
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# NOTE - MYSTERY - just print the input unchanged
awk 1
# or
awk '"a"'

# NOTE - MYSTERY
# you will find several awk programs that use only conditions

# Suppress duplicate lines
awk '!a[$0]++'
# Removes the last field & prints the line
awk 'NF--'
# Prints non blank lines
awk 'NF'
```
