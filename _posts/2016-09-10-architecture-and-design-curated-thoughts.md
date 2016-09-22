---
layout: post
title: Architecture and Design - Some Curated Thoughts
---

## Architecture and Design - Some Curated Thoughts

### Good sides on using micro-services

- breaking large systems into smaller components
- provides handle to control the architecture
- minimise the impact of different style of code due to various teams on a monolith
- provides benefits of using polygot programming languages

<br />

### Side effects on using micro-services

- deal with latency
- negotiate authentication & authorization- workaround for messages that do not arrive leading to async designs
- wide array of testing (unit, functional, security & performance)

<br />

### Microservice - What, Why & How !!

refer - [wix-microservices-devops](https://www.infoq.com/presentations/wix-microservices-devops)

- Each microservice is owned by a team - build it, run it
  - It is not a library, it is a live production system
- It will need its own dev life cycle & operations
- Scale independently
- Use different data store
- Optimize data per use case (Read vs Write)
- Run on different data centers
- System resiliency (degration of service vs. downtime)
- Faster recovery time
- service-to-service communication - json-rpc & http
- service-to-client communication - rest http
- every network hop reduces availability
- service discovery ?
  - zk, consul, etcd, dns + lb ?
- queuing system ?
  - kafka, rabbitmq, threads ?
- api versioning
- failure handling ?
  - retry policy, circuit breaker, throttlers
- expose service metrics within the service itself
- Open sourcing each micro service (e.g. a separate github project) will benefit a lot
 - No need of verbose team communications
 - Consider github to be the communication medium
 - Each issue, commit, doc signals an item for communication when required.
 - Tracking dependencies becomes trivial

<br />

### You must be this TALL to use microservices

refer Martin Fowler

- rapid provisioning
- basic monitoring
- rapid application deployment
- devops culture

<br />

### Micro Services lead to Distributed Systems & hence its challenges

- Configuration Management
- Service Registration & Discovery
- Routing & Load Balancing
- Fault Tolerance
- Monitoring

<br />

### Composing the Micro Services

refer - https://www.youtube.com/watch?v=DBIm6gDpSNg

- to represent the composite system
  - use manifest files
    - but this is very static
  - like BOSH but for micro services
    - without being a centralized management process
    - there will be decentralized teams
    - we just want to build a composite system of micro services
    - we want composing fault tolerant systems
  - Netflix OSS
    - need to learn a lot, pick up & build up the pieces.
  - Spring Cloud (more standardized approach to Netflix OSS)
    - i.e. Spring Cloud on Cloud Foundry
    - Spring Config Server
      - Git <-> Config Server (REST Server) -pull-> App A
      - Git <-> Config Server (REST Server) -pull-> App B
      - Git <-> Config Server (REST Server) -pull-> App B
    - How about update @ realtime ?
      - Cloud Bus (Rabbit MQ)
  - Read Cloud Foundry
    - It might have something good.

<br />

### High Availability vs. Highly Available

- refer [link](http://thenewstack.io/platform9-raises-high-availability-bar-openstack/)

> Berezin explained how using a Red Hat component called Pacemaker to establish clusters and to 
deploy HAProxy load balancers among those clusters, resulted in highly available controllers 
that addressed High Availability needs.

<br />

>  Pacemaker as a cluster resource manager, provided as part of the operating system, that wraps 
itself around services and controls their current state of operation. Together with a virtual
IP address and an HAProxy load balancer, Pacemaker can ensure that some copy of a service, 
somewhere in the network, responds to an API request.

<br />

> Platform9 deploys distributed clustering services on all KVM nodes in that AZ. The clustering 
services use [a] gossip protocol to keep track of all nodes in the cluster. There are actually 
a handful of various gossip protocols currently in use today. One of the first to make serious 
headway was SWIM [PDF], which was supposed to have been an acronym for 
**Scalable Weakly-consistent Infection-style Process Group Membership Protocol**.

<br />

> The whole point of SWIM was to substitute typical, semi-reliable heartbeat protocols with an 
algorithm that periodically measured variances in the chatter messages, or gossip, between 
servers in a cluster about the membership of their various processes.

<br />

> Here, processes in a cluster of servers register for membership. The registration process 
generates streams of messages, which set forth a pattern of normal operation. When a pattern 
changes beyond a continually updated tolerance level, a failure detector component kicks in 
to determine the relative probability of a real failure.

<br />

> The “infection” part is based on a metaphor describing how the membership signals are disseminated 
throughout a cluster. If a “suspicion of failure” message to be multicast to multiple other IP hosts 
in a cluster, there’s a higher chance that the multicast message would not be received by an intended 
recipient, than for the suspicion to be actually correct. That’s because IP multicast was intentionally 
designed to be a “best effort” approach at targeting a broad swath of addresses. “Infection” relies 
upon one member node propensity to chatter with another node. Using a gossip protocol, the suspicions
are piggybacked onto the member nodes’ regular membership chatter messages. Hashicorp's **serf** is an 
implementation of SWIM protocol.

<br />

> Read about VMHA in OpenStack. Refer https://github.com/ntt-sic/masakari

<br />

### Persistent Datastructures

refer - [Intro to ClojureScript](https://www.youtube.com/watch?v=-I5ldi2aJTI)
refer - [Understanding Persistent Vector](http://hypirion.com/musings/understanding-persistent-vector-pt-1)

- immutable values
- structural sharing 
- this is how git works, even the JVM & JS engine use this
- there are not deltas you just point to new tree
- each tree shares structure with old tree

<br />

#### Bitmapped Vector Trie

- invented by Clojure / Rich Hickey
- data lives in the leaves
 - prefix tree used for string lookup
- a bitwise trie
- technically called Persistent Vector
- an array of arrays
- origins of Bitmapped Vector Trie
 - Array Mapped Trie & Hash Array Mapped Trie (Phil Bagwell, 2001)
 - these were mutable datastructures

<br />

#### Clojure's Persistent Vector

- uniform array of arrays of same dimension
- each element of an array is an array
- the depth  = ?
- random access like array
- can add to end like an array
- can iterate with efficiency like an array
- getindex (find)
 - mask the index to find the value
 - if dimension = 4, then mask the first two bits
- dereference the array & do bit masking
- assoc (i.e. update)
 - replace all the arrays in the path till the leaf array
- 32 is the dimension that was selected for Clojure
 - 32^7 implies 7 level deep
 - 34 billion+ elements
 - atmost you need to update 7 arrays to update an element

<br />

#### ReactJS

- tree diffing in-memory
- lazy diffing
- batch the changes... no interleaving

<br />

#### Immutable JS

- idiomatic persistent datastructure library

<br />

### Programming Styles

- [java9-reactive-programming](https://www.infoq.com/presentations/java9-reactive-programming)
  - talks more on Spring Reactive
  - good background on threads to **Future** to **CompletableFuture** to **Streams** to **BackPressure**

<br />

### Quotes

> Microservices architecture is not fundamentally different from SOA; the goals and objectives are the 
same but the approach is slightly refined and in fact, I would simply say that Microservices is mere 
SOA made scalable.

refer - [microservices-distributed-system](https://www.infoq.com/news/2016/09/microservices-distributed-system)

> Thinking in minimal viable products means continuously focusing on adding value to the product by 
implementing the most minimalistic version that offers that value. Once continuous delivery is set up 
accordingly, deploying a new version of altered components can be done for instance on a daily basis, 
of course with CI in place.

refer - [microservices-distributed-system](https://www.infoq.com/news/2016/09/microservices-distributed-system)

> My opinion is that one of the things we’ll realize is the cost of RPC. SoundCloud is using HTTP + JSON for RPC
at the moment and we already did some investment to migrate to Thrift. I believe next step in evolution is to 
migrate all of our services to use a more efficient protocol for RPC.

refer - [microservices-evolution-soundcloud](https://www.infoq.com/articles/microservices-evolution-soundcloud)

### Hands On

- [modularity](https://modularity.org/index.html)
- [stuart sierra - components just enough structure](https://www.youtube.com/watch?v=13cmHf_kt-Q)
- [tesla-microservice](https://github.com/otto-de/tesla-microservice)
- [IMP - clojure-walking-skeleton](http://www.agilityfeat.com/blog/2015/03/clojure-walking-skeleton)
