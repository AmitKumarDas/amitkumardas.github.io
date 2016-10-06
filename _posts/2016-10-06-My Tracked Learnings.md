---
layout: post
title: My Tracked Learnings
---

## My Tracked Learnings


### Code

- [Learn building a pure javascript lib. From bitcore](https://github.com/bitpay/bitcore-lib)
- [Code Style Guide, Pull Requests. From OpenZeppelin](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/CONTRIBUTING.md#style-guidelines)


### Low level Abstracts

- [Linux processes & their status. How ps works](https://fredrb.github.io/2016/10/01/Understanding-proc/)

### Replication, DataStore & CAP

- [Realtime Distributed OLAP Datastore](http://www.slideshare.net/KishoreGopalakrishna/pinot-realtime-distributed-olap-datastore)
- [Sequence of events applied to a state machine](http://bookkeeper.apache.org/)
- [Replicate to multiple servers via replicated log](http://bookkeeper.apache.org/)
- [Database as a replicated state machine. Sum of all updates applied since its initial state](http://bookkeeper.apache.org/)
- [Just replication. No leader election](http://bookkeeper.apache.org/)
- [Want to make database replicated or messaging systems replicated or filesystems replicated ?](http://bookkeeper.apache.org/)
- [Reads & Writes are directed to separate physical disks to keep publish latency as low as possible](https://github.com/yahoo/pulsar)
- [Range Sharding Algorithm - based on table's PK.](https://rethinkdb.com/docs/architecture/)
- [Distribute the shards across the cluster - Evenly !!](https://rethinkdb.com/docs/architecture/)
- [Pick up the correct split point to ensure each shard has roughly similar no of records/data](https://rethinkdb.com/docs/architecture/)
- [All reads & writes are directed to primary replica. Then ordered & evaluated. Data is immediately consistent & conflict-free.](https://rethinkdb.com/docs/architecture/)
- [Neither reads nor writes are guaranteed to succeed if primary replica is unavailable](https://rethinkdb.com/docs/architecture/)
- [Support both up-to-date & out-of-date reads](https://rethinkdb.com/docs/architecture/)
- [Up-to-date read - query routed to shard's primary replica - executed in-order wih other ops on the shard - client sees latest view](https://rethinkdb.com/docs/architecture/)
- [Out-of-date queries have lower latency & stronger availability gurantee](https://rethinkdb.com/docs/architecture/)
- [CAP - high availability - clients can write to the same row on both sides of the netsplit](https://rethinkdb.com/docs/architecture/)
- [CAP - high availability - apps need to deal with clock skew, conflict resolution, conflict repair](https://rethinkdb.com/docs/architecture/)
- [CAP - high availability - perf issues for higly contested keys, latency issues associated with quorums](https://rethinkdb.com/docs/architecture/)
- [CAP - high consistent - also known as Authoritative systems - MongoDB, RethinkDB](https://rethinkdb.com/docs/architecture/)
