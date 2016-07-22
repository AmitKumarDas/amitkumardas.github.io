---
layout: post
title: Call it as basics or tag them as mysteries in shell
---

Call it as basics or tag them as mysteries, clever, etc.. in shell

```bash
#!/bin/bash

# refer : http://stackoverflow.com/questions/10551981/
# refer : http://tldp.org/LDP/abs/html/dblparens.html
# refer : http://stackoverflow.com/questions/4554718/
# refer : http://www.unix.com/302147478-post8.html
# the entire credit goes to above links & its authors

# NOTE - MYSTERY of ((...)) i.e. double parentheses construct
# It permits arithmetic expansion & evaluation
# It allows for c-style manipulation in bash
(( a = 3 ))
(( a ++ ))

# If a less than 40 then 1 else 2
(( t = a<40?1:2 ))

# NOTE - Loop through a string
# ${#var} expands to length of the variable.. Remember $# special variable
# ${var:$i:1} is similar to substring with a start position & a length

myvariable = "abcd"
for((i=0;i<${#myvariable};i++)); do
  echo "${myvariable:$i:1}"
done

# NOTE - Trim the n characters
word = ${word:n}

# NOTE - regex
# If you set shopt -s extglob then you can also use:
# ?() - zero or one occurrence of pattern
# *() - zero or more occurrence of pattern
# +() - one or more occurrence of pattern
# @() - one occurrence of pattern
# !() - anything except the pattern

# NOTE - remove files other than selected
shopt -s extglob
rm !(*test*)

# similar to below in other shells
rm ^*test*

# NOTE - MYSTERY - The () will not work if shopt is not set

# NOTE - command substitution
# this is used to insert the output of one command to another
today=$(date)
echo "$today"

# NOTE - MYSTERY - command substitution vs. parameter expansion
# $(command) vs. ${variable_or_array}
# $(cmd1; cmd2) vs. ${var:n:m}

# NOTE - localizing effects
# within a bash script use command substitution $(...) to localize the effects
# ${BASH_VERSINFO[*]: 0:3} extracts the first 3 elements from array
# here the array variable is $BASH_VERSINFO
# NOTE - Parameter Expansion happens before echo is called
ver=$(IFS=.; echo "${BASH_VERSINFO[*]: 0:3}")  

```
