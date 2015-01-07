---
layout: post
title: "Testing multithreaded code in Java"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Testing multithreaded code is a tough endeavour. The first advice that you get when try to test concurrency is to isolate your concurrent concerns in the code as much as possible. This a general design advice but in this case it's even more mandatory. When you have properly unit tested the logic that is wrapped by the concurrent construct, you can then start thinking in how to test that bit. Otherwise you can spend so many time trying to figure out what's the concurrent problem, being in the end a simple flawed business logic.
