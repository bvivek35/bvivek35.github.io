---
layout: post
title:  "Memcached Internals Part-2 - Networking"
date:   2018-11-10 17:45:00 -0800
categories: technical memcached cache design networking
author: Vivek Bharadwaj
---

[Memcached][memcached-link] is a simple Key-Value ~~store~~ cache designed to be high performant and intended to augment a data store. In this series of posts, I will be talking about different aspects of this system. 

This is part-2 of the series and deals with networking in Memcached.  
[Part-1][memcached-part-1] deals with Intro to Memcached.

##### Networking in Memcached

Memcached uses Libevent to be notified when a connection is a) ready to be accpeted b) ready to be read from. We'll go over how this happens in some depth a little later. Importantly, Libevent provides an event loop and threads register for events `EV_READ` on a FD (File Descriptor). When the event occurs, a callback provided at the time of registration is invoked. 

Coming to the protocol side of things, Memcached allows for using both TCP and UDP as it's Transport Layer. The Application Layer uses one of two custom protocols:
1. ASCII Protocol: Simple text exchange between client/server.
2. Binary Protocol: Copmlex protocol with lots of knobs and switches.

For the case of this series, I'll be using the ASCII protocol since it's very simple to understand and is very human-friendly.

##### What Happens at Startup?

On startup, Memcached will create a socket, bind it and listen for incoming connections on this socket. This server-socket registers for EV_READ on Libevent. 
The Threading subsytem will be initalized which will spawn threads and each of these worker threads will open a pipe: this pipe allows for the server-socket to wake up the worker and hand over the client.

![memcached-startup-networking]({{"/assets/posts/2018-11-10-memcached-networking/memcached-startup-networking.png" | absolute_url}}){:height="50px"}

##### Handing Over Client Socket

When a client initiates connection to Memcached, the server-socket hands over the connection to a thread. This thread will register for EV_READ on the just recieved socket fd. This conn object will be used for drive a Finite State Machine.

![memcached-networking]({{"/assets/posts/2018-11-10-memcached-networking/memcached-networking.png" | absolute_url}}){:height="50px"}



##### Syscalls and Programming Style

Going over the source code of Memcached, one thing becomes very apparent. NO COPY of data is ever encouraged, but in case of `realloc` a buffer. Naturally, the question of copying response to buffers come up. Memcached uses a technique called Scatter/Gather for networking operations. Essentially, we read data into a series of buffers of varying length and let the kernel assemble the final message to be transmitted. Explaining this is not in the scope of this post, but lookup `sendmsg(2)` on your man pages.

##### Roadmap For This Series

In the next post, I'll talk about issues when augmenting Memcached with a data store. We'll see what race conditions occur and how to avoid them to maintain Read-After-Write consistency.

Au Revoir

[memcached-link]: https://memcached.org/
[memcached-part-1]: /2018/10/15/memcached-Intro/