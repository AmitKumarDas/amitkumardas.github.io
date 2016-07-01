---
layout: post
title: Making your awk act like grep
---

Making your awk act like grep

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows its power
# the power of functional awk vs. all those imperative languages.

# if pattern matches the entire sentence then print the entire sentence
awk '{if ($0 ~ /pattern/) print $0}'

# rewrite by pushing pattern outside of action
# i.e. awk 'pattern {action}'
awk '$0 ~ /pattern/ {print $0}'

# remove redundancy
# i.e. print $0 === print & pattern is matched against the entire statement
awk '/pattern/ {print}'

# remove further redundancy i.e. print is the default action
# Look it is similar to grep now.
awk '/pattern/'
```
