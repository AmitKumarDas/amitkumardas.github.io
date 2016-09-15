---
layout: post
title: Lessons learned in a storage startup
---

## Problem we were trying to solve at CloudByte

We started off with the core idea on building QOS (Quality of Service) for
storage. We knew the managed service providers had a tough time sharing
infrastructure across businesses. The usual approach to solve this noisy neighbour
problem was to provision these customers in isolated infrastructures
(read storage servers). This was obviously not cost effective. Our QOS was built
on throttling the server resources read RAM, IOPS, throughput, etc to their
configured thresholds.

<br />

## Issues faced

However, we realized that we cannot sell this as-is unless we have an end-to-end
solution for storage. Hence, in addition to modifying an existing file system,
FreeBSD OS kernel modules, we also had to build/hack a middle-ware, management,
UI console and plugins to numerous infrastructure management solutions (such as
OpenStack, CloudStack, Docker, etc). This was already too much to handle for a
small startup like us. In addition, most of the spendings went into buying different
hardware (we needed to have a Hardware Compatibility List after all).

<br />

## Mistakes Made

There were a lot of them that we made. I have listed down the important ones here. 

> We were getting multiple requests from probable customers which never materialized
in reality. Since the requests were more of big features. Prioritizing customer needs
vs. product needs went into a toss. We had often started to rely more on people than 
on technology to solve our needs.

<br />

> Though we were using open source libraries, our contributions itself was closed
sourced. The only open sourcing that we did as a company was when my orchestration
team submitted relevant volume plugins to OpenStack & CloudStack communities. We
later realized that being open source is an important factor to solve the issue
of vendor lock-in. We even had to lose a customer on grounds of being a closed
source company. This happened even after various rounds of successful POCs with
the same customer.

<br />

### My transformation after joining a startup

Prior to joining CloudByte, I was a typical Java architect dealing with web
application development, low latency trading systems & later with security products.
In CloudByte, I started off with modularising the UI code (JavaScript) that was
becoming unwieldy. Then I was off to build plugins for infrastructure solutions
such as CloudStack, VMWare, & OpenStack. With the ecosystem of heterogeneous solutions
needed for our core, we had the urgent need to automate the entire stack. Automation
was needed for our continuous testing, delivery & integration platform. Soon I
started developing DSLs (Domain Specific Languages) for our automation (read DevOps)
needs. The plan was to have anyone to start scripting out her manual tasks. This
mandated the scripting language to be easy to learn & adapt so that QA, support,
or even any customer admin would be able to automate without reaching out for new
tool or asking for feature requests. Currently, I am building the management solution
for containerized storage (based on Linux). All these stuff made me use various 
programming languages starting from Java to JavaScript to Python to Groovy to Bash to
Golang. This also resulted in learning better tools & un-learning my older approaches to
solving a problem. This is what I suppose has transformed me into a full stack
programmer.
