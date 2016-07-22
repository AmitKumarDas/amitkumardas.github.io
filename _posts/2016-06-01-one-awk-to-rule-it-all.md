---
layout: post
title: One awk to rule it all
---

One awk to rule it all

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# below one-liner represents a series of commands separated by unix pipe
# get the first line, filter foo, if found replace foo with bar,
# then translate any small letter characters with capital letters
# finally split it by ' ' & display second field
somecommand \
  | head -n +1 \
  | grep foo \
  | sed 's/foo/bar/' \
  | tr '[a-z]' '[A-Z]' \
  | cut -d ' ' -f 2

# now we do the same stuff using only awk
somecommand \
  | awk 'NR==1 && /foo/ {sub(/foo/,"bar"); print toupper($2)}'

```
