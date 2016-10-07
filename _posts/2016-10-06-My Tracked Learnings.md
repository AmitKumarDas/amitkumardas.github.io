---
layout: post
title: My Tracked Learnings
---

## My Tracked Learnings

> These are pinned down lines from various references !! A sort of filtering of 
*`the good parts`* from various articles. These curated thoughts will you ample 
ideas & time to design a solution to any problem definition. Hence, two parts of 
the dimension i.e. ideas & time are taken care of. You need to strategize on the
solutioning part.

<br />

### Good Coding

- [Learn building a pure javascript lib. From bitcore](https://github.com/bitpay/bitcore-lib)
- [Code Style Guide, Pull Requests. From OpenZeppelin](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/CONTRIBUTING.md#style-guidelines)

<br />

### Low level Abstracts

- [Linux processes & their status. How ps works](https://fredrb.github.io/2016/10/01/Understanding-proc/)

<br />

### Replication, DataStore & CAP

- [Realtime Distributed OLAP Datastore](http://www.slideshare.net/KishoreGopalakrishna/pinot-realtime-distributed-olap-datastore)
- [Sequence of events applied to a state machine](http://bookkeeper.apache.org/)
- [Replicate to multiple servers via replicated log](http://bookkeeper.apache.org/)
- [Database as a replicated state machine. Sum of all updates applied since its initial state](http://bookkeeper.apache.org/)
- [Just replication. No leader election](http://bookkeeper.apache.org/)
- [Want to make database replicated or messaging systems replicated or filesystems replicated ?](http://bookkeeper.apache.org/)
- [Reads & Writes are directed to *`separate physical disks`* to keep publish latency as low as possible](https://github.com/yahoo/pulsar)
- [Range Sharding Algorithm - based on table's PK.](https://rethinkdb.com/docs/architecture/)
- [Distribute the shards across the cluster - Evenly !!](https://rethinkdb.com/docs/architecture/)
- [Pick up the correct split point to ensure each shard has roughly similar no of records/data](https://rethinkdb.com/docs/architecture/)
- [All reads & writes are directed to primary replica. Then ordered & evaluated. Data is immediately consistent & conflict-free.](https://rethinkdb.com/docs/architecture/)
- [Neither reads nor writes are guaranteed to succeed if primary replica is unavailable](https://rethinkdb.com/docs/architecture/)
- [Support both up-to-date & out-of-date reads](https://rethinkdb.com/docs/architecture/)
- [Up-to-date read - query routed to shard's primary replica - executed in-order wih other ops on the shard - client sees latest view](https://rethinkdb.com/docs/architecture/)
- [Out-of-date queries have lower latency & stronger availability gurantee](https://rethinkdb.com/docs/architecture/)

<br />

### [CAP - High Availability](https://rethinkdb.com/docs/architecture/)

- Clients can write to the same row on both sides of the netsplit
- Apps need to deal with clock skew, conflict resolution, conflict repair
- Perf issues for higly contested keys, latency issues associated with quorums

<br />

### [CAP - High Consistentency](https://rethinkdb.com/docs/architecture/)

- Also known as Authoritative systems - MongoDB, RethinkDB

<br />

### [Medium Engg Stack](https://medium.engineering/the-stack-that-helped-medium-drive-2-6-millennia-of-reading-time-e56801f7c492#.1fpa19iya)

- Node has worked well, but performance problems have emerged where we block the event loop. 
  - To alleviate this, we run multiple instances per machine &
  - route expensive endpoints to specific instances, thus isolating them.
- We use a combination of *`Nginx and HAProxy as reverse proxies and load balancers`*
- DynamoDB is still our primary datastore, but it hasn’t been completely smooth sailing. 
  - We have a *`Redis cache cluster sitting in front of Dynamo`*, which mitigates these *`issues with reads`*.
- We use Neo4J (a master with 2 replicas) to store relations between the entities
  - Edges are created on entity creation & when users perform action, we walk the graph to filter & recommend posts
- Apache Spark will be the tool of our choice for data pipelines / ETL
- We use Protocol Buffers for our schemas to comm across all layers of distributed system
- Image servers are written in go & uses groupcache which is a memcache alternative
  - In-memory cache is *`backed by a persistent`* S3 cache

<br />

### [linkerd](https://blog.buoyant.io/2016/10/04/a-service-mesh-for-kubernetes-part-i-top-line-service-metrics/)

- A service mesh is a layer that manages the communication between apps (or between parts of the same app, e.g. microservices). 
- In traditional apps, this logic is built directly into the application itself: 
  - retries and timeouts, monitoring/visibility, tracing, service discovery, etc. are *`all hard-coded into each application`*.
- A service mesh like linkerd provides critical features to multi-service applications running at scale:
  - Top-line service metrics: 
    - Success rates, request volumes, and latencies.
  - Baseline resilience: 
    - Retry, deadlines, circuit-breaking.
  - Latency and failure tolerance: 
    - Failure and latency aware load balancing that can route around slow or broken service instances.
  - Distributed tracing a la Zipkin and OpenTracing
  - Service discovery: locate destination instances.
  - Protocol upgrades: 
    - Wrapping cross-network communication in TLS, or converting HTTP/1.1 to HTTP/2.0.
  - Routing: 
    - Route requests between different versions of services, failover between clusters, etc.
- Supports common protocol formats:
  - HTTP/1.1
  - Thrift
  - Mux

### [Beyond round robin load balancing](https://blog.buoyant.io/2016/03/16/beyond-round-robin-load-balancing-for-latency/)

- Fundamental to the notion of scalability
- Traffic distribution is the act of load balancing
- Provides another feature i.e. resiliency
  - i.e. failure of individual components will not fail the system
  - thus distribute the traffic to healthier instances
- Must protect against latency like it protects against failure
  - Even with the presence of slow replicas, the system as a whole must remain fast
  - So how do you ditribute load across replicas with varying speeds ?
    - peak EWMA (exponentially weighted moving average)
    - round robin (most common in *Nginx & HAProxy*)
    - least loaded
