---
layout: post
title: OpenStack CloudByte cinder driver configuration
---

# OpenStack CloudByte cinder driver configuration

- Refer below link for details:
  - [CloudByte driver configuration](http://cloudbytestorages.github.io/blog/general/setup/openstack/2016/05/11/OpenStack-Configuration-With-CloudByte-Cinder-Driver.html)

<br />

```
# open the cinder.conf & verify the settings mentioned below.
# change the values of CloudByte specific settings based on your environment.
# CloudByte specific settings start with "cb_" as its prefix.

> vi /etc/cinder/cinder.conf

..
[DEFAULT]
default_volume_type = cb-gold
enabled_backends = cloudbyte-gold
iscsi_helper = tgtadm
..

[cloudbyte-gold]
volume_driver = cinder.volume.drivers.cloudbyte.cloudbyte.CloudByteISCSIDriver
volume_backend_name = cloudbyte-gold
san_ip = 20.10.43.1
san_login = admin
san_password = na
cb_tsm_name = OPENSTACK_VSM
cb_account_name = OPENSTACK_ACC
cb_apikey = gaxFrefF7h_-SUxVPODDYsvGxZwtn0jnMChUeElNcIcZzP1XNw8YGKhcvCO46AqJCXsHVePmaJcBNYydagREmw
cb_confirm_volume_create_retries = 15
cb_confirm_volume_create_retry_interval = 5
cb_confirm_volume_delete_retries = 15
cb_confirm_volume_delete_retry_interval = 5
cb_add_qosgroup = latency:15,graceallowed:true,iopscontrol:true,memlimit:0,tpcontrol:false,throughput:0,iops:20,networkspeed:0
cb_create_volume = compression:off,deduplication:off,blocklength:512B,sync:always,protocoltype:ISCSI,recordsize:4k
cb_initiator_group_name = ALL
..

```
