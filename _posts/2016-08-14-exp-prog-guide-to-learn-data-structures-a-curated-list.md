---
layout: post
title: Experienced programmer's guide to learn data structures - A curated list
---

## Java's Concurrent HashMap - Internals

refer - [Concurrent HashMap - Internals](https://dzone.com/articles/how-concurrenthashmap-works-internally-in-java)

- **Part of the map** gets locked during mutation
- This part of the map is called Segment. There are many segments inside a map.
  - e.g. if there are 32 segments in a map, 
  - then each segment can be operated by a unique thread concurrently
- 
