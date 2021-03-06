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
- [Build Technical Wealth](http://firstround.com/review/forget-technical-debt-heres-how-to-build-technical-wealth/?mc_cid=07889699da&mc_eid=9a1ca6920c)

<br />

### Solutioning

- [Ready made lists for solutioning !!!](https://monkey.org/~marius/hints.pdf)
- [aws guides](https://github.com/open-guides/og-aws)

<br />

### Low level Abstracts

- [Linux processes & their status. How ps works](https://fredrb.github.io/2016/10/01/Understanding-proc/)
- [Network Performance Debugging](https://linkerd.io/in-depth/network-performance/)
- [Docker Security](http://rhelblog.redhat.com/2016/10/17/secure-your-containers-with-this-one-weird-trick/)

<br />

### [Protocols](https://monkey.org/~marius/hints.pdf)

- Embrace RPCs
- Language agnositic means to serialize data:
  - Thrift, Protocol Buffers, Avro
- Use one that employs schema.
- Only JSON does not employ schema

<br />

### [Measure Liberally](https://monkey.org/~marius/hints.pdf)

- request_latency_ms can have various *dimensions*
  - avg, count, max, min, p50, p90, p95, p99, p999, p9999, sum
- Stats are aggregated across:
  - clusters, datacenters
- pay special attention to outliers, look at distributions
- Profile & trace systems in situ

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

### Kubernetes Issues Encountered by Others

- [kubernetes caveats and workarounds](http://code.haleby.se/2016/03/04/kubernetes-caveats-and-workarounds/)

<br />

### [Immutable Containers](https://docs.openshift.org/latest/whats_new/index.html)

- Image consists of a particular snapshot of app code & its dependent libs
  - Configuration, secrets, or persistent data is attached to the container at runtime

<br />

### CoreOS/flannel

- Kubernetes does away with port mappings
- Assigns unique IP to a pod
- Configurations:
  - Step 1 - Overlay network range: 10.100.0.0/16
  - Step 2 - Size of subnet for each host: /24
  - Hence, hosts may be registered with 10.100.5.0/24, 10.100.12.0/24 & so on
  - These registrations has a lease for 24 hours
  - These lease can be extended
- Routing table encapsulation is done via:
  - Utilizing the Universal TAP/TUN devices & proxies the IP fragments between:
    - the TUN device & a UDP socket
- Flannel uses etcd to maintain the mapping between allocated subnets & real host IP address
- Flannel uses UDP for data path
  - UDP encapsulates the datagrams to transmit them to remote host
  - UDP can pass through the firewalls easily

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

<br />

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
- Finagle operates at Layer 5 in the OSI model (the “session” layer):
  - it has access to information such as queue depth and RPC latencies.
- Load balancing performance goes beyond the choice of algorithm:
  - An algorithm may be unsuitable for long polling
- For systems that load balance higher-level connections such as RPC or HTTP calls:
  - where Layer 5 information such as endpoint latencies and request depths are available, 
  - round robin load balancing can perform worse than other algorithms in the presence of slow endpoints.

<br />

### [Hints for your design - Fault Tolerance](https://monkey.org/~marius/hints.pdf)

- What happens when a dependency starts failing ?
- How can the system degrade in a graceful manner ?
- How does the system react to overload ?
- What's the worst-case scenario for total failure ?
- How quickly can the system recover >
- Is delayable work delayed ?
- *`Is the system as simple as possible ?`*
- How can the system shed load ?
- Which failures can be mitigated ?
- Which operations can be retried ?

### [Hints for your design - Scalability](https://monkey.org/~marius/hints.pdf)

- How does the system grow ?
  - What is the chief metric with which the system scales ?
- How does the system scale to multiple datacenters ?
- How does the demand vary ?
  - How do you ensure the system is always able to handle peak loads ?
- How much query processing is done ?
  - is it possible to shape data into queries ?
- is the system replicated ?

<br />

### [Hints for your design - Operability](https://monkey.org/~marius/hints.pdf)

- How can features be turned on or off ?
- How do you monitor the system ?
  - How do you detect anomaly ?
- Does the system have operational needs specific to the application ?
- How do you deploy the system?
  - How do you deploy in an emergency ?
- What are the capacity needs ?
  - How does the system grow ?
- How do you configure the system ?
  - Can you configure quickly ?
  - Is it template based ?
- Does the system behave in a predictable manner ?
  - Where are there nonlinearities in load or failure responses ?

<br />

### [Hints for your design - Efficiency](https://monkey.org/~marius/hints.pdf)

- Is it possible to precompute data ?
- Are you doing as *little work as possible* ?
- Is the program as concurrent as possible ?
- Does the system make use of work batching ?
- Have you profiled the system ?
  - Is it possible to profile in situ ?
- Are there opportunities for parallelization ?
- Can you load test the system ?
  - How do you detect performance regressions ?

<br />

### Private IP addresses to communicate with external Public network

- Configure the firewall for IP masquerading
- NOTE - firewall's internal IP device allows private IPs to communicate with each other
- NOTE - firewall's external IP device is eth0
- Masquerading masks the requests from private IP address with the IP address of eth0
  - Hence, private IPs are able to communicate to external/public IPs
- *`iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE`*
  - this rule uses the *NAT packet matching table*
  - specifies the built-in *POSTROUTING chain for NAT*
  - on the firewall's external IP device i.e. eth0
  - -j MASQUERADE masks the private IP of a node etc with the IP address of eth0


<br />

### Networking - Subnet Basics

```bash
10.0.0.0        -   10.255.255.255  (10/8 prefix)
172.16.0.0      -   172.31.255.255  (172.16/12 prefix)
192.168.0.0     -   192.168.255.255 (192.168/16 prefix)
```

- if confused with ifconfig to understand the subnet try below
  - `ip -4 a | grep eth0`
- How do you know if its a bridge network ?
  - If the broadcast is 0.0.0.0 ?
- Alternatively the dumb way is to use nm-tool
  - It will give you the details that can be understood by a layman !!
  - Address, Prefix (i.e. Subnet), Gateway, DNS, State (i.e. Status)

<br />

### Architecture Diagrams

- [Legacy vs. Current](https://monkey.org/~marius/hints.pdf)

<br />

### [GraphQL will replace REST](https://dev.to/reactiveconf/why-i-believe-graphql-will-come-to-replace-rest)

<br />

### [OneOps](http://www.oneops.com/benefits.html)


