---
layout: post
title: Datastructures - Some Curated Thoughts
---

## Datastructures - Some Curated Thoughts

### Persistent Datastructures

- refer - [Intro to ClojureScript](https://www.youtube.com/watch?v=-I5ldi2aJTI)
- refer - [Understanding Persistent Vector](http://hypirion.com/musings/understanding-persistent-vector-pt-1)

#### What are these ?

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

#### >>> vs. >> in Java

- >>> is a logical shift
  - any bits shifted in will be zero
- >> is arithmetic shift
  - bits shifted in will have the same value as the most significant bit
- For C/C++/GO, >>> is same as >> for unsigned types

<br />

#### Big O calculation

- How has Clojure done this in O(1) ?
- Lets understand the branching factor i.e. no of children per node.
  - With 2 children per node, this will be O(log n) of base 2
  - Clojure has 32 kids per node
  - The depth can be calculated based on the size required
  - depth of 6 with 32 branching factor can provide around a billion elements
  - The Big O for Clojure then comes to O(log n) with a base of 32 which is known as O(log n)
- The shifts & masks are some of the most efficient operations on a modern CPU
  - How about the cache misses ?
  - It is handled by JVM

<br />

#### Lets understand RB Tree before learning a Trie

- elements are in the interior nodes
- find is done via comparison of element/key at the **current node**
  - if element is lower than the node, we brach left, if higher we branch right
- **leaves** are **nil** & doesnot contain anything

<br />

#### Trie

- has all values stored in its leaves
- picking the **right branch** is done by using parts of the key as a lookup
- Clojure's Persistent Vector is a trie where the indices of elements are used as keys
  - The catch here is to split these indices integers
  - Spliting these integers is done via digit partitioning or its faster sibling bit partitioning
  - e.g. the key 2345 can be split as [2,3,4,5] list
  - we also can use any base we want to for the key
  - this list is used for lookup or insertion in the trie
  - when we reach at the last digit by traversing the digits of the list, we get our required value
- Digit partitioned tries would need a couple of integer divisions & modulo operations
  - Doing this on every branch will be time consuming
  - All digit partitioned tries in a base which is a power of two (2, 4, 8, 16, 32, etc)
  - can be turned into a bit-partitioned ones.
  - Bit manipulation can save us from costly arithmetic operations i.e. division & modulo
  - For 32-way branching trie, we need 5 bits in each part
  - For 4-way branching trie, we need 2 bits

<br />

#### Sample Trie

- Assume a 4-way branching
- So we need a 2 bit partitioning
- Assume a Trie of 887 elements
- So each child of the root node need to contain at least 256 elements 
  - 256 == 1 << 8 i.e. shift = 8
  - Hence levels will be 8 -> 6 -> 4 -> 2 -> 0
  - Alternatively, 256 elements with 4 way paritioning means 4 elems * 4 elems * 4 elems * 4 elems i.e. 4 levels depth is required
- The mask here will be 4 - 1 = 3
- TASK - Get the element at 626 i.e. 0000 0010 0111 0010 in its binary representation ?
  - Set node = root
  - set level = 8 // used for shifting
  - set BITS = 2
  - hop by levels 
  - logic::
    - position = (key >>> level) & MASK
    - node = node[position]
    - level =- BITS
    - loop till level = 0
  - value = node[position]
