---
layout: post
title: "Freedom vs Power"
description: ""
category: 
tags: [concurrency, philosophy, functional programming, oop]
---
{% include JB/setup %}

We usually think about freedom in positive terms. A tool is able to achieve something because is free to do something. What is even more interesting is thinking the other way around. A tool is able to achieve something, or at least better, faster or stronger, thanks to some limitation in its freedom. Let's see some general examples before of getting into programming arena:

* The elbow, as part of the arm, provides more strength and control thanks to its limited movement range. 
* A train is more convenient than a car if the journey is straightforward. The lack of freedom in the train path makes it faster and more efficient.
* A paradoxical example is included in politic liberalism. That ideology tries to maximise the freedom of every human being, but pretty much every liberalism flavour supports some kind of freedom restriction. For instance, taxes remove the individual freedom of spending in whatever we want, but if we reinvert that money in children education, we'll maximise the future freedom of those kids. Theory and practice say that if a society is full of educated and free men, that society will enjoy more freedom.

Programming history is full of freedom restrictions. In fact, every technique based in abstractions is based in power through limitations. We, as programmers, really know that it's not possible, not desirable, to understand deeply every step of a simple computation. Every layer, from OS to high level frameworks, hides complexity and restricts freedom. As noted in this Uncle Bob's post, programming paradigms can be understood through it's more notable restrictions:

* Structured programming: forbids goto use, making programs easier to reason.
* Object oriented programming: encapsulates state and behaviour into objects. That encapsulation could be suggested or dictated, dependending on the language, but is a restriction anyway.
* Functional programming: removes state making data inmutable. That lack of freedom frees us of the burden of locking and concurrent programming subtleties in general.

One of the perceived Java success comparing with C or C++ was delegating the memory management to the infrastructure. I've never written any professional code that has to take care about its own memory, and to be honest, I'm quite happy about that.

With functional constructs included in languages like Scala or Java we'll see dissapear other 'low level' concerns like operations over collections. The idea behind high order functions like map, filter, reduce or foreach is hiding the iteration mechanism, allowing a programming style cleaner and with less bugs.

Another candidate to dissapear into the infrastructure beast is concurrency. Let's define concurrenty as the program ability to use several CPUS interleaved or at the same time (paralellism would more appropiate in this case). The details of how many entities (threads, actors...) can work simultaneously, how to retrieve its results, when to discard them and, if they work on some shared resource, how to synchronize them, it's quite hard, and makes me think about hiding that stuff like we did with memory management.

Nowadays there are so many platforms that tries to overcome the concurrency pain restricting what programmers can do. Different approaches are more suitable for different domains, and we will talk about them in future posts.   

 
