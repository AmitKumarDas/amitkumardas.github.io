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

### Programming Styles

- [java9-reactive-programming](https://www.infoq.com/presentations/java9-reactive-programming)
  - talks more on Spring Reactive
  - good background on threads to Future to CompletableFuture to Streams to BackPressure

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
