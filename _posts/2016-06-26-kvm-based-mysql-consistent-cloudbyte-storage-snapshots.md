---
layout: post
title: KVM based MySQL consistent CloudByte storage snapshots - WIP
---

KVM based MySQL consistent CloudByte storage snapshots - **WIP**

> This article does not focus on why should we have consistent snapshots.
> Rather it tries to collage information on how to do it right for MySQL
> application running on a KVM based hypervisor.

Setup assumptions:

```
- OpenStack Liberty single node setup
- Ubuntu 14.04 as KVM based hypervisor (will host OpenStack)
- Libvirt (version - ) on hypervisor
- CentOS VM (version - )
- mysql application (version - ) running in CentOS VM
- CloudByte storage volumes will be iSCSI based
```

OpenStack & CloudByte cinder driver setup:

```
# /etc/cinder/cinder.conf
```


OpenStack commands:

```
# Create a volume that acts as the root disk for the CentOS VM
cinder create rootos 40

- Create a volume that acts as the installation location of mysql
cinder create mysqlstor 40

# Create a volume that will hold the mysql operational data.
cinder create mysqldata 40

# Create a glance image with libvirt & qemu guest agent settings

# Spawn a VM that will use above image & volumes

```

Libvirt & QEMU considerations:

```
```

MySQL filesystem considerations:

```
# Filesystem for system partitions.
# For MySQL partition, ext3 or ext4 are fine,
# However, xfs may give better performance. It can deal better with concurrent I/O.
```

MySQL bin & data directory considerations

```
# Let /mnt/mysqldata be the mountpoint directory for MySQL data storage
# Let /mnt/mysql be the mountpoint directory for MySQL installation & bin logs

# Create a data directory under /mnt/mysqldata mountpoint
cd /mnt/mysqldata && mkdir datadir

# Check the ownership of the directories to be mysql user

# Edit my.cnf to have below
datadir -> /mnt/mysqldata/datadir
log-bin -> /mnt/mysql/binlog/mysql-bin
relay-log -> /mnt/mysql/binlog/mysql-relay
```

MySQL IO scheduler considerations

```
# Do we need to care about IO scheduler ?
# Linux uses CFQ for IO scheduling.
# CFQ does not care about IO latency & may serialize requests i.e. slow DB performance
```

Monitoring during snapshot:

> What is the motive behind capturing metrics? This is done to aid the
> DB Admin as well as the storage infrastructure Admin in the future while
> selecting a storage snapshot that is best fit to be restored. In addition,
> proper understanding of these metrics might help us to abort the snapshot
> procedure.

```
- Capture system metrics of the VM:
  - Linux load
  - CPU usage
  - Memory usage
  - swap usage  
  - network bandwidth
  - disk usage
- Capture disk metrics of the VM:
  - Read/Write requests i.e. IOPS
  - IO queue length i.e. operations waiting for disk access
  - Average IO wait i.e. time to wait in the queue before disk access
  - Average Read/Write time i.e. time to finish disk operation i.e. latency
  - Read/Write bandwidth i.e. data transfer from & to the disk
- Capture MySQL metrics:
  - MySQL swap usage
  - uptime
  - threads connected
  - max used connections
  - aborted_connects
- Capture MySQL errors:
  - Presence of errors in mysql.log file
  - Any log files deleted but file descriptor is still open
- Capture MySQL queries:
  - Slow queries; they generate excessive disk reads, memory, & CPU usage.
  - No of temp disk tables i.e. temp tables created on disks than RAM
    - this is typically done for joins
  - full table scans
- Capture MySQL cache, buffers & locks
  - innodb row lock waits i.e. no. of times to wait before locking a row
  - innodb buffer pool wait free i.e. no. of times to wait before flushing memory pages
  - open tables; impacts the table cache size & file descriptors available to mysql user
  - long running transactions
  - deadlocks
```

Overall snapshot procedure:

```
- Setup a mysql QEMU hook in the VM.
- Run IO on mysql application using HammerDB tool.
- Use innotop (on a remote client) to continuously monitor the mysql application.
- Record the IOPS usage @ CloudByte server for the specific volumes.
- Freeze the VM from the hypervisor using libvirt API.
- Take a libvirt instance snapshot & store it in root volume itself.
- Take CloudByte snapshots of all the volumes of the VM.
- Thaw the VM from the hypervisor using libvirt API.
- Stop the monitoring tool.
- Stop the IOPS recording @ CloudByte server.
- Stop the web server.
- Fetch the snapshot id(s) of the snapshots that were taken during freeze.
- Log the captured metrics along with the snapshot id in a structured format.
```

Commands for snapshotting:

```
```

Overall restore procedure:

```
- Gracefully shutdown the VM.
- Restore all the snapshots on this VM.
- Restore the libvirt instance snapshot on the root volume.
- Verify if the VM starts up.
- Verify if mysql is operational.
- Verify the data integrity of mysql.
```

Commands for restoration:

```
```

References:

- [how to monitor mysql](https://blog.serverdensity.com/how-to-monitor-mysql/)
- [monitor mysql using innotop](https://www.percona.com/blog/2013/10/14/innotop-real-time-advanced-investigation-tool-mysql/)
