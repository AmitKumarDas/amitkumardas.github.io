---
layout: post
title: Using rerun
---

# What is ```rerun``` ?

> **rerun** is a simple framework that turns loose shell scripts into
> modular automation. Rerun will help you organize your scripts into user friendly
> commands. This is the site for [rerun](http://rerun.github.io/rerun/).

- rerun has a module >> command hierarchy.
- One can list the available modules.
- One can list the available commands within a module.
- Finally one can run a command of a module.

<br />

## Installing rerun on CentOS

```bash

$ uname -a
Linux localhost 3.10.0-327.el7.x86_64 ... x86_64 x86_64 x86_64 GNU/Linux

$ yum install git

$ git --version
git version 1.8.3.1

$ cd $HOME
$ git clone git://github.com/rerun/rerun.git
$ cd rerun
```

<br />

##  System wide setup as we shall use rerun for system related task

```bash

# step 1
$ cp -r rerun/ /usr/bin/

# step 2
# create a nested folder structure /rerun/modules @ /var

# step 3
$ cp -r rerun/modules/stubbs/ /var/rerun/modules/

# step 4
$ cd /etc/profile.d/

# Create a file called rerun.sh @ /etc/profile.d
# Ensure below in a file called rerun.sh
export PATH=$PATH:/usr/bin/rerun
export RERUN_MODULES=/var/rerun/modules
export RERUN_COLOR=false
[ -r /usr/bin/rerun/etc/bash_completion.sh ] && source /usr/bin/rerun/etc/bash_completion.sh

# Step 5 - logout & re-login
```

<br />

##  User specific setup

```bash
# Assumptions - [optional] - Above steps are set for system wide settings
# Assumptions - login using a user other than root.

# Step 1 - edit ~/.bash_profile & ensure below
export PATH=$PATH:/usr/bin/rerun
export RERUN_MODULES=/var/rerun/modules
[ -t 0 ] && export RERUN_COLOR=true
[ -r /usr/bin/rerun/etc/bash_completion.sh ] && source /usr/bin/rerun/etc/bash_completion.sh

# Step 2 - logout & re-login
```

<br />

## Adding rerun module(s), function(s) & option(s)

```bash
$ rerun
Available modules:
  stubbs: "Simple rerun module builder" - 1.3.0 (/var/rerun/modules/stubbs)

$ rerun stubbs: add-module
...

$ rerun stubbs: add-command
...

$ rerun stubbs: add-option
...
```
