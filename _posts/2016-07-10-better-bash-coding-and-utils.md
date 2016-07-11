---
layout: post
title: Better Bash Coding & Utils
---

# Better bash coding & utils

> These information has been collected from numerous articles & libraries. They
> have been mentioned in the references section. This article does not mention why
> it has to be done in a particular manner. In order to dive deep into the details
> one needs to refer to the references' links.

<br />

## One liner bash tips

- Be explicit rather than being terse.
  - If implemented effectively can challenge any of the modern day functional languages.
  - You never know if an expert or a newbie maintains this code.
- Use ```{}``` when referencing variables, preferring ${NAME} instead of $NAME.
- Prefix ```_``` for internal variable & function names.
- Treat the output of a command like a file via ```<(some command)```.
- Verify your script via [shellcheck](http://www.shellcheck.net/)

<br />

## More bash tips

```bash

# left trim & right trim
${var%suffix-pattern}         # the suffix is trimmed out
${var#prefix-pattern}         # the prefix is trimmed out

# Assuming var=foo.pdf
echo ${var%.pdf}.txt          # prints foo.txt
```

<br />

```bash

$ string=01234567890abcdefgh

$ echo ${string:7}
7890abcdefgh

$ echo ${string:7:2}
78

$ echo ${string: -7}
bcdefgh
```

<br />

```bash
$ array[0]=01234567890abcdefgh

$ echo ${array[0]:7}
7890abcdefgh

$ echo ${array[0]:7:0}

$ echo ${array[0]:7:-2}
7890abcdef

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

$ echo ${array[@]: -7:-2}
bash: -2: substring expression < 0

$ echo ${array[@]:0}
0 1 2 3 4 5 6 7 8 9 0 a b c d e f g h

$ echo ${array[@]:0:2}
0 1

$ echo ${array[@]: -7:0}
```

<br />

```bash

# Use () to group commands
# do something in current dir
(cd /some/other/dir && other-command)
# continue in original dir
```

<br />

```bash

# Arithmetic expansions within (())
i=$(( (i + 1) % 5 ))
```

<br />

```bash

# Avoid re-typing
mv foo.{txt,pdf} some-dir   # moves both food.txt & foo.pdf
cp somefile{,.bak}          # expands to somefile & somefile.bak

# creates a directory tree of all combinations
mkdir -p test-{a,b,c}/subtest-{1,2,3}
```

<br />

```bash

# Positional variables & parameter expansions:

${1:-alternative}       # default value: the string 'alternative'
${2:-}                  # blank default value
${1:?'error message'}   # exit with 'error message' if variable is unbound
```

<br />

```bash

# Arrays & parameter expansions:

${some_array[@]:-}              # blank default value
${some_array[*]:-}              # blank default value
${some_array[0]:-}              # blank default value
${some_array[0]:-default_value} # default value: the string 'default_value'
```

<br />

## Unix tools

- Read the ```Art of command line``` for numerous unix tools.
  - datamash - to transpose file content, reverse fields of a file, statistics.
  - When to use rsync instead of scp.
  - Use glances for current system analysis.
  - For deep system performance analysis - use sysdig

<br />

## References

- [bash boilerplate](https://github.com/alphabetum/bash-boilerplate)
- [Art of command line](https://github.com/jlevy/the-art-of-command-line)
- [Shell parameter expansion](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)
