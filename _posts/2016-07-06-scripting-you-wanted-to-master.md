---
layout: post
title: Scripting that you wanted to master
---

# Scripting that you wanted to master


## Named Pipes

- [A reference to Named Pipes](http://www.linuxjournal.com/article/2156?page=0,1)

```bash
# e.g. vi <(ls -l) does following
# 1. ls -l is run in a subshell
# 2. the output of above subshell is redirected to a pipe (managed by bash)
# 3. vi will then open up the fd (as pipe is a fd i.e. /dev/fs/63)
```

<br />

## Seemingly advanced yet simple usage of 'tee' & 'named pipes'

```bash
# Try to understand this logic:

$ ls | tee >(grep foo | wc >foo.count) \
           >(grep bar | wc >bar.count) \
     | grep baz | wc >baz.count
```

<br />

## Another seemingly complex yet simple named pipes with LISP style syntax

```bash
# cat <(cat <(cat <(ls -l))))
```

<br />

## Convert stdout of one command to command line args of another.

```bash
# xargs is the right tool in this case.
# xargs can be used to find contents within files.
# very useful in piping & creating one-liner scripts
# may not be much used if using awk.

# -d is the delimeter option
# use -n to split as per your convenience
# > echo "abc1.txt" | xargs rm -rf

# You could also convert this some, using the **command substitution** syntax $()
# i.e. LISP style
# > echo $(ls)
```

<br />

## Mapping / Merging two std outputs via awk -- LISP style

```bash
# > awk 'NR==FNR {a[$1]=$2;next} {print a[$1] " " $2}' <(echo '001 amit') <(echo '001 das')
# amit das
```
