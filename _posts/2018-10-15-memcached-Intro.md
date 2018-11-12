---
layout: post
title:  "Memcached Internals Part-1 - Intro"
date:   2018-10-15 17:45:00 -0800
categories: technical memcached cache design
author: Vivek Bharadwaj
---

[Memcached][memcached-link] is a simple Key-Value ~~store~~ cache designed to be high performant and intended to augment a data store. In this series of posts, I will be talking about different aspects of this system. 

This is part-1 of the series and deals with understanding the problem Memcached is trying to solve and how to use it. 

##### Simple Intro to Memcached

Memcached is a caching middleware that allows for very fast lookup and insertion of Key-Value pairs. Intended to scale applications where datastore is a bottleneck, typically web scale applications, Memcached offers to cache query results, enabling applications to avoid the data store. 

With Memcached, the Key-Value pairs are simply a sequence of bytes of arbitrary length. It doesn't offer any data structure (ahem Redis), the interpretation is left to the consumer. Memcached is simply a cache of bytes looked up by a key (of bytes). 

##### Is It a Store or a Cache?

I think it's important to distinguish these two classes of software. We are familiar with a Store - Databases (SQL and NoSQL alike). There is a guarantee that, all the data is available once commited. However, a cache is by design and definition is not strictly required to hold onto the data. It may at it's will evict entries from the memory. 

A store has a connotation of a persistent underlying storage. A cache is simply there to hold some KV pairs. It's never expected to replace a datastore. Simply augment it. 

A store is slow for lookup/insert while changes are durable.
A cache is fast for lookup/insert while changes need not be durable.

##### Using Memcached to augment - Policies
How does one begin to use a cache to augment the data store?
Note that all reads go to the cache and the data store (if a MISS).
1. Write-Around: The application deletes the dirty KV pair and update the data store. The next read will be a miss- which will populate the cache.
2. Write-Through: The application sets KV pair in both the cache and data store. 
3. Write-Back: The dirty KV pair is buffered in the cache to be written to the the store at a later time. Based on policy, these buffered writes are applied to the data store. 
Obviously, each has it's pros and cons, but observe the relative complexity of each solution. Write-Back is the most complex to implement, but writes need to buffer to the cache which is quick(very).
![write-policy]({{"/assets/posts/2018-10-15-memcached-intro/write-policy.jpg" | absolute_url}}){:height="50px"}


##### Memcached Parts - Components



##### Roadmap For This Series
There are several components/layers to any software project, I'll be going over them one at a time.
1. Networking - How are requests communicated? How is request data sent up the stack?
2. Memory Management - How are KV pairs stored? What's the allocation strategy? What to do when memory is low?
3. What are some important structures involved?

After learning about the layers and components, I'll talk about issues when augmenting Memcached - Race conditions, achieving Read-After-Write Consistency, Calcification, eviction policy, what to do when caches go down?

In the next post, we'll examine the networking layer of memcached. How does it accept requests? what are the protocols? who runs the request? Is Memcached MultiThreaded?

Au Revoir

[memcached-link]: https://memcached.org/