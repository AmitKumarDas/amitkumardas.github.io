---
layout: post
title: Crash consistent snapshots of VMs created using OpenStack & libvirt on KVM
---

# Crash consistent CloudByte snapshots of VMs created using OpenStack & libvirt on KVM based hypervisor

<br />

## Scripting Goodness - II ```Snapshotting on top of CloudByte ElastiCenter API```

> This article focuses on script based CRUD operations w.r.t a CloudByte volume
> snapshot. This will focus on use of cURL & json libraries to meet the desired
> purpose. One might want to refresh the basics by reading through below mentioned
> links.

- [Refresh Link 1](https://amitkumardas.github.io/2016/07/07/scripting-openstack-cli.html)
- [Refresh Link 2](https://amitkumardas.github.io/2016/07/01/using-curl-as-rest-client-to-cloudbyte-elasticenter.html)
- [Refresh link 3](https://amitkumardas.github.io/2016/07/02/using-curl-and-json-to-create-a-cloudbyte-volume.html)

<br />

## Task: ```Setup Environment```

```bash
# install NodeJS & npm
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs

$ node -v
v6.3.0

$ npm -v
3.10.3

# install json package
$ sudo npm install -g json

$ json --version
json 9.0.4
written by Trent Mick
https://github.com/trentm/json

$ curl --version
curl 7.35.0
```

<br />

## Task: ```Setup ElastiCenter```

- Setup the environment to make cURL requests to ElastiCenter

```bash

$ pwd
/home/cbos/ec

$ cat ec20.10.43.1
url=https://20.10.43.1/client/api

$ cat eckey20.10.43.1
apiKey=gaxFrefF7h_-SUxVPODDYsvGxZwtn0jnMChUeElNcIcZzP1XNw8YGKhcvC&response=json
```

<br />

## Task 1 : ```Create a snapshot```

- Create a crash consistent CloudByte volume snapshot of a given Nova instance.
- Steps:
  - ```Step 1``` - display the list of
    - (Nova Instance ID, Virsh Instance ID, Cinder Volume ID)
  - ```Step 2``` - freeze the Virsh instance
  - ```Step 3``` - create a CloudByte volume snapshot of the bootable volume.
  - ```Step 4``` - thaw the Virsh instance
  - ```Step 5``` - display the snapshot details

<br />

### Task 1 : ```Step 1```

```bash

# Assumptions: A simple command will display the list
# Nova Instance ID, Virsh Instance ID, Cinder Volume ID

$ awk 'BEGIN {print "Nova_ID Virsh_ID Cinder_ID"} \
       NR==FNR {a[$2]=$1;next} a[$1] {print $0 " " a[$1]}' \
    <(cinder list \
        | awk ' NF > 1 && $2 != "ID" && $12 ~ /^cb-/ && $14=="true" \
            {print $2 " " $18}') \
    <(nova list \
        | awk 'NF > 1 && $2 != "ID" {print $2 }' \
        | nova show $(awk '{print $1}') \
        | awk '$2=="OS-EXT-SRV-ATTR:instance_name" {virsh_id=$4} $2=="id" {nova_id=$4} \
            END {print nova_id " " virsh_id}') \
  | column -t

Nova_ID                               Virsh_ID           Cinder_ID
0b7724e2-fc8a-420c-a20a-7da85329e419  instance-00000003  dd280c48-f5cc-4e86-a866-4cff0bf330ec
```

<br />

### Task 1 : ```Step 2```

```bash
$ sudo virsh domfsfreeze instance-00000003
Froze 1 filesystem(s)
```

<br />

### Task 1: ```Step 3```

```bash

# Assumptions: Cinder Volume ID is provided as input.
# NOTE - Cinder volume id will be sanitized from hyphen '-'.
# NOTE - CloudByte snapshot id & name are the output.

$ curl -K ec20.10.43.1 \
    -d @eckey20.10.43.1 -G -k -s -S \
    -d command=listFileSystem \
  | json listFilesystemResponse.filesystem \
  | json -a id name \
  | awk '$2=="dd280c48f5cc4e86a8664cff0bf330ec" {print "id="$1}' \
  | curl -K ec20.10.43.1 \
    -d @eckey20.10.43.1 -G -k -s -S \
    -d command=createStorageSnapshot \
    -d name=mysnappy2 \
    -d @- \
  | json createStorageSnapshotResponse.StorageSnapshot \
  | json -a id name
```

<br />

### Task 1: ```Step 4```

```bash
$ sudo virsh domfsthaw instance-00000003
Thawed 1 filesystem(s)
```

<br />

### Task 1: ```Step 5```

```bash

```

<br />

## Task 2: ```List snapshots```

- List CloudByte volume snapshots of a given Nova instance.
- List CloudByte volume snapshots of all Nova instances.
- Steps:
  - display the entire list
    - (Nova Instance ID, Nova Instance Name, Cinder Volume ID, CB Snapshot ID)
  - or
  - display the list for a particular Nova Instance ID
    - (Nova Instance ID, Nova Instance Name, Cinder Volume ID, CB Snapshot ID)

```bash

```

<br />

## Task 3: ```Rollback to a snapshot```

- Rollback (a Nova instance) to a particular CloudByte volume snapshot.

<br />

## Task 4: ```Delete a snapshot```

- Delete a CloudByte volume snapshot (of a particular Nova instance).
