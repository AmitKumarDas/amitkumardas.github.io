---
layout: post
title: Making sense from two related files via awk
---

Making sense from two related files via awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows its power
# the power of functional awk vs. all those imperative languages.

# NR==FNR implies the first file only
# NR implies no of records & is not reset after finishing a file & reading the next one
# FNR indicates no of records & is always 1 at the start of a file
# Note the use of next in the first sequence's action;
# it implies corresponding sequences will not be triggered
# other condition & {other action} is executed for second file only
awk 'NR==FNR { # some actions; next} # other condition {# other actions}' file1 file2

# prints lines that are both in file1 and file2 (intersection)
awk 'NR==FNR{a[$0];next} $0 in a' file1 file2

# a data file with $NF i.e. last field as op code
# 20081010 1123 xxx
# 20081011 1234 def
# 20081012 0933 xyz
# 20081013 0512 abc
# 20081013 0717 def

# a mapping file, that maps op codes to descriptions
# abc withdrawal
# def payment
# xyz deposit
# xxx balance

# use information from a map file to modify a data file
awk 'NR==FNR{a[$1]=$2;next} {$3=a[$3]}1' mapfile datafile

# replace each number with its difference from the maximum
# here the same file is provided twice
# this technique can be used to calculate mean, average & other maths stuff
awk 'NR==FNR{if($0>max) max=$0;next} {$0=max-$0}1' file file
```
