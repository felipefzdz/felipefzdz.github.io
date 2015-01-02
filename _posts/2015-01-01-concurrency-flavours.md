---
layout: post
title: "Concurrency flavours"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Teaching can be really hard. Sometimes can be even most enjoyable than really doing stuff, but sometimes can be a pain in the ass. The latter usually happens when you have to face the truth. You don't really know something that you should know, and even worst, you did think that you knew it!

The last year I've been helping my girlfriend to move from Marketing to Programming. She enrolled into some bootcamp course (kudos to General Assembly guys, nice work), so she has been learning from really basics in little time. When you teach someone really newbie, you realise how big is the knowledge that you have (or you should have) to perform properly any basic development task. Modern platforms hide complexity making our lifes easier, but with a caveat: we could program without having any idea of what we're doing.

Trying to simplify as much as possible the involved concepts in programming, I enforced binay tuples when teaching basics to my girlfriend.

1. Input and output. Every program could be thought as a black box with some inputs and outputs. That's why we write them, someone gives data that wants to manipulate through some automatism.
2. Data and behaviour. The input/output tuple is data and the black box is the behaviour. We don't just move data, we transform it.
3. Storage and cpu. Those are the physical correspondences of data/behaviour tuple. Almost everybody understand that memory or hard drive are finites, and it's easy to spot the idea of every cpu is a guy that transforms data.

Obviously there are so many more tuples, but for the topic of this post is enough. We'll use concurrency as an umbrella for pure or simulated paralellism. Concurrency will be then the ability of using simultaneously different cpus. Just a remainder, we want cpus not for its own sake, but to manipulate data. If that data is mutable, we'll have a new diffent set of problems.

Let's try to understand one of the most important concurrency problems, having cpus idles when there is still pending work. That could happens because of:

1. The concurrent entity (being a thread, an actor, a fiber... let's say from now CE) is waiting to access to some shared resource that has a lock on it. One approach to overcome those waiting times is making inmutable the data. As we're not going to update any data, but keep creating them as needed, the obvious trade-off will be the space constraint.
2. The CE is waiting for some blocking IO. Network, filesystem and different sources of IO are orders of magnitude slower than normal operations, so if we use a blocking platform, our CEs will likely wait on that. One approach is the event loop implemented by NodeJs. In that event there is only one CE and every IO call is async, so we won't lose time waiting for the the result. That callback management is donde under the hood, so we won't manage any CEs created by Node platform. It's possible to spawn some CE if we need to do some CPU intensive operation.
3. The CE pool features are not optimized for our resources. Tuning the size and characteristics of our CE pool can be quite tricky. If we use a conservative approach we could have CPUs doing nothing, and if we're too aggresive, our system could spend most of the time switching between CEs. Memory consumption could be an issue as well. Fibers or actors claim to be much more lightweight than threads.

It seems that old style of blocking, locking, user-managed thread style of concurrency feels quite dated for nowaday challenges. Anyway the 'new' approaches bring with them new caveats. Async programming is inherently more complex. Callback is hell is well known in Node ecosystem. Promises are a great advance, but they are not as intuitive as a simple sync workflow. Inmutable data can lead to simpler programs, but has trade-offs as explained before.

My experience with classic multithreaded programs has been quite painful. Testing is complex and diagnosing problems is one of the toughest problems. So to be honest, as I'm happy about not having to manage my own memory, and I'm keen to see a future with more an more platforms that hide some of those concurrent hassles. 
