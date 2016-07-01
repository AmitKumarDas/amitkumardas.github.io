---
layout: post
title: OpenStack CloudByte cinder code refresh
---


# OpenStack CloudByte cinder code refresh

- This is required if we want the hot patches, fixes from CloudByte team.
- These may not be present in upstream OpenStack maintained repository.

<br />

```
# check for the presence of 3 files named:
# cloudbyte.py, options.py & __init__.py at below location

> cd /opt/stack/cinder/cinder/volume/drivers/cloudbyte

# Get the liberty cinder drier with latest hot fixes from below location:
# https://github.com/CloudByteStorages/openstack/tree/master/liberty/cloudbyte

# Once the 3 files are replaced, the cinder services needs to be restarted

> cd /home/cbos/devstack
> ./rejoin-stack.sh

# use Ctrl + a + p -> previous screen
# use Ctrl + a + n -> next screen
# use Ctrl + a + d -> detach i.e. exit

# multiple instances of cinder-api services leads to bind errors

RuntimeError: Could not bind to 0.0.0.0:8776 after trying for 30 seconds
ERROR cinder Traceback (most recent call last):
ERROR cinder   File "/usr/local/bin/cinder-api", line 10, in <module>
ERROR cinder     sys.exit(main())
ERROR cinder   File "/opt/stack/cinder/cinder/cmd/api.py", line 56, in main
ERROR cinder     server = service.WSGIService('osapi_volume')
ERROR cinder   File "/opt/stack/cinder/cinder/service.py", line 377, in __init__
ERROR cinder     port=self.port)
ERROR cinder   File "/opt/stack/cinder/cinder/wsgi/eventlet_server.py", line 176,
                 in __init__
ERROR cinder     {'host': host, 'port': port})
ERROR cinder RuntimeError: Could not bind to 0.0.0.0:8776 after trying for 30 seconds

# verify the no of cinder services that are running in the devstack box
ps -aux | grep cinder | grep -v grep

# There are commonly 3 cinder services that will be running (there are more actually).
# Common ones are: cinder-scheduler, cinder-volume & cinder-api
# Kill all the cinder services if above bind error occurs
# kill -9 <pid>

# navigate to screen & start these services
> screen -x

# in c-api screen run below commmand to start cinder-api
/usr/local/bin/cinder-api --config-file /etc/cinder/cinder.conf

# in c-sch screen run below commmand to start cinder-scheduler
/usr/local/bin/cinder-scheduler --config-file /etc/cinder/cinder.conf

# in c-vol screen run below commmand to start cinder-volume
/usr/local/bin/cinder-volume --config-file /etc/cinder/cinder.conf

# verify the logs in each cinder screens

# detach from screen
> Ctrl + a + d
```
