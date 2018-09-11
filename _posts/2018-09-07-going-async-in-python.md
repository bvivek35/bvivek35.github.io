---
layout: post
title:  "Going Async in Python"
date:   2018-09-07 11:08:00 -0800
categories: technical python asyncio async await event-loop
author: Vivek Bharadwaj
---

In this post, I will be giving a briefing on async programming styles and dive into Python's offerings in this regard. I'll cover the basics of asyncio module and show code on how to get going with asyncio. A lot of what asyncio builds on top of is generators and co-routines. While it is important to know the base, we'll keep them at bay. I find it much easier to tackle problems at a certain abstraction level and async/await is a convenient abstraction level to learn at. 

##### Concurrency with a single thread - Event Loop

One way to think of concurrency is an interleaved execution model. In a typical program we have one thread executing one function. This is true always. Suppose this function executes a blocking call, instead of sitting idle on the CPU waiting, it can relinquish the CPU and request to be run once the blocking call returns. The function will be suspended and executed at a later time. We'll use a queue to maintain a list of these functions to be run later.

Typically this thread of execution is called an event loop. In that, it loops over a queue of tasks and completes tasks. Of course, a task can insert itself back into the queue or insert additional tasks into the queue. 

On some level, this seems familiar to OS concepts of process scheduling and management. Instead of processes, we deal with functions/tasks. The OS has control of when these switches happen, but in userland the programmer should have this control. 
The JavaScript engines (think Chrome V8, NodeJs, etc) use event loop as well.

In the next section we'll look at how to do this in Python. 

##### Python AsyncIO

In Python v3.5, two new keywords were added: async, await. These let us convert a blocking call to a 'relinquish control now and schedule when call returns.' This enables us to write programs where CPU is not left idle waiting for a blocking call to return (disk io or network). Invoking a blocking call can be thought of as a break away point, execute the blocking call and once it's done - return to me. 

Defining a function with `async` tells the interpreter that this function must be run on an event loop.
When someone calls an async function, it simply returns an object that can be thought of as a token. When the callee returns, the caller uses this token to figure out the return value. This is similar to a future. 
Now, to call an async function, we must `await` for it. This in turn tells the interpreter that the event when the blocking call finishes - return to this function.

The fact that async functions can call other async functions seems natural. Can a regular function call an async function? The answer is no. We'll leave discussion on why this is the case in this tutorial. 

We have got ourselves into a dilemma here. The regular Python interpreter runs in "non async" and we cannot call async functions from there. There seems to be two different worlds - async funtions and non-async functions. How do we bring them together? We simply execute the async functions on an event loop and this event loop can be any thread of execution.

##### Talk's Cheap, Show Me the Code
Use Python v3.5+

```python
# Good Ol' fib
def fib(n):
    if n == 0 or n == 1:
        return n
    
    return fib(n-1) + fib(n-2)


# Async version of fib
# Observe the async before the "def" 
# This indicates async_fib is an async fn and can be run on an ev loop.
async def async_fib(n):
    if n == 0 or n == 1:
        return n
    
    # As mentioned earlier, async calls should be awaited.
    # async_fib(n-1) is a recursive call to an async fn
    # Rules:
    # 1) Should be made from an async fn
    # 2) Use await statement to get the exec and get return value
    # Missing await is a common error. The runtime will print a warning, but it's easy to miss and mess.
    # async_fib(n-1) will be scheduled to run on the event loop, the same as the caller async_fib(n).
    return await async_fib(n-1) + await async_fib(n-2)

# Running regular fib
# Regular function invocation
print(fib(10))

# Running async fib
# Needs to be run on an event loop. 
# Python having given us async/await also provides an event loop
# It's all in the new asyncio module
import asyncio
loop = asyncio.get_event_loop()

# Turns out, this is not going to call / run this function
# to_run is actually a coroutine (we totally skipped this part)
# This is the object that can be 
# 1) awaited - Expression (await to_run)
#              Needs to be in an async function ARGH!!!
# 2) Run on the event loop
to_run = async_fib(10)

# run_until_complete will run the coroutine till it yields no more value. 
# For the purpose of our discussion, we'll assume that this method will simply run the function
result = loop.run_until_complete(to_run)

print(result)
```

As you can see, we have barely scratched the surface of async/await. In the coming posts, I'll be talking about the internals (SPOILER ALERT - coroutines and generators). 

Au Revoir