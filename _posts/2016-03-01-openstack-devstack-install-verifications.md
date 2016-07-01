---
layout: post
title: OpenStack devstack install verifications
---


# OpenStack devstack install verifications


Refer to below link for installation details:
- [link](http://cloudbytestorages.github.io/blog/general/setup/openstack/2016/05/11/OpenStack-Configuration-With-CloudByte-Cinder-Driver.html)


```
# Assuming, the devstack liberty has been installed

> cd /home/cbos/devstack

> git status
On branch stable/liberty
Your branch is up-to-date with 'origin/stable/liberty'.

> cinder list
ERROR: You must provide a user name through --os-username or env[OS_USERNAME].

# The answer lies in understanding the openrc bash file.
# Find it under the devstack installed location.
# This file sources various bash files like functions, stackrc, etc.

# It also sources an environment file named ".stackenv".
# It has the necessary password & other services specific settings for devstack.

# need to set the username & tenant name via "openrc" bash file.
> source openrc admin admin

> cinder list
# will provide some list of volumes if already created
```
