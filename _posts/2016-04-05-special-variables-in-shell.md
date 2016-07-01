---
layout: post
title: Special variables in shell
---

```bash
#!/bin/bash

# refer : http://www.tutorialspoint.com/unix/unix-special-variables.htm
# the entire credit goes to above link & its author

# file name of the current script
$0

# arguments of the script i.e. positional arguments
$1
# .. till $n

# no of arguments supplied to the script
$#

# wild card that equals all the arguments
$*

# wild card that equals all the arguments & keeps them separate
$@

# exit status of last command executed
$?

# the process number of the current shell
$$

# the process number of the last background command
$!
```
