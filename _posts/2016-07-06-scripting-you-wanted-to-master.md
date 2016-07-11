---
layout: post
title: Scripting that you wanted to master
---

# Scripting that you wanted to master

<br />

## Expansion using positional parameters

- [Shell parameter expansion](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)

```bash

$ string=01234567890abcdefgh

$ echo ${string:7}
7890abcdefgh

$ echo ${string:7:0}

$ echo ${string:7:2}
78

$ echo ${string:7:-2}
7890abcdef

$ echo ${string: -7}
bcdefgh

$ echo ${string: -7:0}

$ echo ${string: -7:2}
bc

$ echo ${string: -7:-2}
bcdef
```

<br />

```bash
$ array[0]=01234567890abcdefgh

$ echo ${array[0]:7}
7890abcdefgh

$ echo ${array[0]:7:0}

$ echo ${array[0]:7:2}
78

$ echo ${array[0]:7:-2}
7890abcdef

$ echo ${array[0]: -7}
bcdefgh

$ echo ${array[0]: -7:0}

$ echo ${array[0]: -7:2}
bc

$ echo ${array[0]: -7:-2}
bcdef
```

<br />

```bash
$ array=(0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h)

$ echo ${array[@]:7}
7 8 9 0 a b c d e f g h

$ echo ${array[@]:7:2}
7 8

$ echo ${array[@]: -7:2}
b c

$ echo ${array[@]: -7:-2}
bash: -2: substring expression < 0

$ echo ${array[@]:0}
0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h

$ echo ${array[@]:0:2}
0 1

$ echo ${array[@]: -7:0}
```

<br />

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
# Try to understand below logic:

$ cat <(cat <(cat <(ls -l))))
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
$ echo "abc1.txt" | xargs rm -rf

# You could also convert this some, using the **command substitution** syntax $()
# i.e. LISP style
$ echo $(ls)
```

<br />

## Mapping / Merging two std outputs via awk -- LISP style

```bash

$ awk 'NR==FNR {a[$1]=$2;next} {print a[$1] " " $2}' <(echo '001 amit') <(echo '001 das')
amit das
```
