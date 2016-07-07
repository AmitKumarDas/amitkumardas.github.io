---
layout: post
title: Scripting OpenStack's cli
---

# Scripting OpenStack's cli

<br />

## Scripting Goodness - I ```Scripting on top of OpenStack's cli```

<br />

## Task - Display Nova instance id.

- Typical output of 'nova list':
  - ID, Name, Status, Task State, Power State, & Networks

```bash

# Extract the nova instance IDs
> nova list \
  | awk 'NF > 1 && $2 != "ID" {print $2 }'

0b7724e2-fc8a-420c-a20a-7da85329e419
```

<br />

## Task - Display volume id, volume type, bootable flag & attached to instance fields.

- Typical output of 'cinder list':
  - ID, Status, Migration Status, Name, Size,
  - Volume Type, Bootable, Multiattach,
  - Attached To.

```bash

# display volume id, volume type, bootable flag & attached to instance

$ cinder list \
  | awk ' NF > 1 && $2 != "ID" {print $2 " " $12 " " $14 " " $18}' \
  | column -t
dd280c48-f5cc-4e86-a866-4cff0bf330ec cb-gold true 0b7724e2-fc8a-420c-a20a-7da85329e419
```

<br />

## Task - Map ElastiStor boot volumes with Nova instances.

```bash

# ASSUMPTION - ElastiStor volumes have volume-type starting with 'cb-'

$ cinder list \
  | awk ' NF > 1 && $2 != "ID" && $12 ~ /^cb-/ && $14 {print $2 " " $18}' \
  | column -t
dd280c48-f5cc-4e86-a866-4cff0bf330ec 0b7724e2-fc8a-420c-a20a-7da85329e419
```

<br />

## Task - Map Nova instances with Virsh instances.

```bash

# Map Nova instances with Virsh instances
$ nova list \
  | awk 'NF > 1 && $2 != "ID" {print $2 }' \
  | nova show $(awk '{print $1}') \
  | awk '$2=="OS-EXT-SRV-ATTR:instance_name" {virsh_id=$4} $2=="id" {nova_id=$4} \
      END {print nova_id " " virsh_id}' \
  | column -t
```

<br />

## Task - Map Nova instances with Virsh instances with ElastiStor boot volumes.

```bash

# reference design used to implement this task
# 1. use of named pipes
# 2. mapping two related named pipes via awk
# easy to understand sample
$ awk '1' <(echo "hi") <(echo "bye")
hi
bye

# Map Nova instances with Virsh instances with ElastiStor boot volumes
$ awk 'NR==FNR {a[$2]=$1;next} a[$1] {print $0 " " a[$1]}' \
  <(cinder list | awk ' NF > 1 && $2 != "ID" && $12 ~ /^cb-/ && $14 {print $2 " " $18}') \
  <(nova list | awk 'NF > 1 && $2 != "ID" {print $2 }' \
    | nova show $(awk '{print $1}') \
    | awk '$2=="OS-EXT-SRV-ATTR:instance_name" {virsh_id=$4} $2=="id" {nova_id=$4} \
      END {print nova_id " " virsh_id}') \
  | column -t


# pretty printed format
$ awk 'BEGIN {print "Nova_ID Virsh_ID Volume_ID"} \
        NR==FNR {a[$2]=$1;next} a[$1] {print $0 " " a[$1]}' \
  <(cinder list | awk ' NF > 1 && $2 != "ID" && $12 ~ /^cb-/ && $14 {print $2 " " $18}') \
  <(nova list | awk 'NF > 1 && $2 != "ID" {print $2 }' \
    | nova show $(awk '{print $1}') \
    | awk '$2=="OS-EXT-SRV-ATTR:instance_name" {virsh_id=$4} $2=="id" {nova_id=$4} \
      END {print nova_id " " virsh_id}') \
  | column -t
```
