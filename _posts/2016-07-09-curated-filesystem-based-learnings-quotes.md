---
layout: post
title: Curated filesystem based learnings/quotes
---

# Curated filesystem based learnings/quotes

<br />

## Original links:

- [ayende](https://ayende.com/blog)
- [Magic Pocket](https://blogs.dropbox.com/tech/2016/05/inside-the-magic-pocket/)

<br />

## Do not trust the hardware

> ...there is the paranoid mode for durability, in which you don’t trust the hardware,
> so you write to multiple locations, hash it and validate..., I think that this
> is the purpose of the file system to verify that, and this is where the
> responsibility of the database ends

<br />

## Disks & their IOPS

> ...A high end (15,000 RPM) hard disk can do a theoretical maximum of 250 such writes
> per second, and that is an extremely theoretical number. In most cases, even on
> high end hard disks, you’ll see a maximum of a 100 – 150 per second. You can
> probably double or triple that for high end SSD drive, but those are still very
> poor numbers, compared to the number of operations you can do in memory and in
> the CPU in that time frame.

<br />

## Durability via COW

> ... instead of modifying the data in place, we write it (typically at the end
> of the file), fsync that, then point to the new location from a new file, and
> fsync that in turn. Breaking it apart into two fsyncs means that it is much more
> costly, but it also forces the file system to put explicit barriers between
> the operations, so it can’t reorder things.

<br />

## Durability via explicit journalling

> ...idea is that you dedicate a specific file (or set of files), and then you
> can just write to them as you go along. Each transaction you write is hashed
> and verified, so both the header and the data can be checked at read time.
> And after every transaction, you’ll fsync the journal, which is how you commit
> the transaction.

<br />

## Immutability

> ..Magic Pocket is an immutable block storage system. It stores encrypted chunks
> of files up to 4 megabytes in size, and once a block is written to the system
> it never changes. Immutability makes our lives a lot easier.

<br />

## Immutability with changes

> ..When a user makes changes to a file on Dropbox we record all of the alterations
> in a separate system called FileJournal. This enables us to have the simplicity
> of storing immutable blocks while moving the logic that supports mutability
> higher up in the stack.

<br />

## Durability is the key

> ..This data is erasure-coded for efficiency and stored across multiple geographic
> regions with a wide degree of replication to ensure protection against calamities
> and natural disasters.

## Scaling issues

> ..traffic grew suddenly and we started saturating the routers between our network
> clusters. This required us to change our data placement algorithms and our request
> routing to better reflect cluster affinity (along with available storage capacity,
> cluster growth schedules, etc) and eventually to change our inter-cluster network
> architecture altogether.

<br />

## Simplicity

> ..There were times when we could have opted for a distributed hash table or trie
> for our Block Index and instead just opted for a giant sharded MySQL cluster;
> this turned out to be a really great decision in terms of simplifying development
> and minimizing unknowns.

## What size is good size ?

> ..**4MB** is a pretty small amount of data in a multi-exabyte storage system however
> and too small a unit of granularity to move around whenever we need to replace
> a disk or erasure code some data. To make this problem tractable, we aggregate
> these blocks into **1GB** logical storage containers called buckets. The blocks
> within a given bucket don’t necessarily have anything in common; they’re just
> blocks that happened to be uploaded around the same time.

## What about replication ?

> ..
