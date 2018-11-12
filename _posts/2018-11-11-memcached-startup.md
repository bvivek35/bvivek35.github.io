---
layout: post
title:  "Memcached Internals Part-1 - Startup"
date:   2018-11-11 17:45:00 -0800
categories: technical memcached cache design
author: Vivek Bharadwaj
---

[Memcached][memcached-link] is a simple Key-Value ~~store~~ cache designed to be high performant and intended to augment a data store. In this series of posts, I will be talking about different aspects of this system. 

This is part-1 of the series and deals with 1,000 ft. view of the design and understand the problem Memcached is trying to solve. 

##### Simple Intro to Memcached

Memcached is a caching middleware that allows for very fast lookup and insertion of Key-Value pairs. Intended to scale applications where datastore is a bottleneck, typically web scale applications, Memcached offers to cache query results, enabling applications to avoid the data store. 

With Memcached, the Key-Value pairs are simply a sequence of bytes of arbitrary length. It doesn't offer any data structure (ahem Redis), the interpretation is left to the consumer. Memcached is simply a cache of bytes looked up by a key (of bytes). 

##### Is It a Store or a Cache?

I think it's important to distinguish these two classes of software. We are familiar with a Store - Databases (SQL and NoSQL alike). There is a guarantee that, all the data is available once commited. However, a cache is by design and definition is not strictly required to hold onto the data. It may at it's will evict entries from the memory. 

A store has a connotation of a persistent underlying storage. A cache is simply there to hold some KV pairs. It's never expected to replace a datastore. Simply augment it. 

A store is slow for lookup/insert while changes are durable.
A cache is fast for lookup/insert while changes need not be durable.

##### Memcached Parts - Components

1. Networking - How are requests communicated? How is request data sent up the stack?
2. Memory Management - How are KV pairs stored? What's the allocation strategy? What to do when memory is low?
3. What are some important structures involved?

##### Roadmap For This Series

In the next post, we'll examine the networking layer of memcached. How does it accept requests? what are the protocols? who runs the request? Is Memcached MultiThreaded?

After learning about the layers and components, I'll talk about issues when augmenting Memcached - Race conditions, achieving Read-After-Write Consistency, Calcification, eviction policy, what to do when caches go down?

Au Revoir

[memcached-link]: https://memcached.org/