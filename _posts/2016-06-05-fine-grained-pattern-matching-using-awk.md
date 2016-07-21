---
layout: post
title: Fine grained pattern matching using awk
---

Fine grained pattern matching using awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows the power of awk
# the power of functional awk vs. all those imperative languages.

# NOTE - An example to matching lines starting with FOO1, or FOO2, or ... FOO9
awk '/^FOO[1-9]*/'

# NOTE - Below shows range based filtering power of awk
# NOTE - It prints the **lines** between the patterns,
# inclusive of the pattern matching lines
cat ls.txt
# CUnitAutomated-Listing.xml
# CUnitAutomated-Results.xml
# cunit-to-junit.pl
# Desktop
# Documents
# Downloads

cat ls.txt | awk '/D/,/s/'
# Desktop
# Documents
# Downloads

# NOTE - Below shows the way to exclude the patterns while printing
cat ls.txt
# Hi Hello
# Bye Bye

# NOTE - excludes the starting match
cat ls.txt | awk '/Hi/,/Bye/ {if (!/Hi/) print}'
# Bye Bye

# NOTE - excludes both the starting & ending match
# NOTE - Since there are no lines within the matching lines, nothing is printed
cat ls.txt | awk '/Hi/,/Bye/ {if (!/Hi/&&!/Bye/) print}'
#

# NOTE - Below shows the terse power of awk in doing the above stuff
# NOTE - Below prints the lines within the matching ranges; matched lines excluded
somecmd | awk '/endpattern/{p=0};p;/beginpattern{p=1}'

# NOTE - Prints the lines within the matching ranges; excluding the endpattern
somecmd | awk '/beginpattern/{p=1} /endpattern/{p=0} p'

# NOTE - Prints the lines within the matching ranges; excluding the beginpattern
somecmd | awk 'p; /endpattern/{p=0} /beginpattern/{p=1}'
```
