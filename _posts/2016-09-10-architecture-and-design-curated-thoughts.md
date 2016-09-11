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

### Side effects on using micro-services

- deal with latency
- negotiate authentication & authorization
- workaround for messages that do not arrive leading to async designs
- wide array of testing (unit, functional, security & performance)

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
