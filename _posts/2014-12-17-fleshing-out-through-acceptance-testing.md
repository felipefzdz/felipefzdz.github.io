---
layout: post
title: "Fleshing out through acceptance testing"
description: ""
category: 
tags: [bdd, acceptance testing, architecture, testing]
---
{% include JB/setup %}

Now that we have decided how our code is going to be structured from a high level perspective, let's think how we're going to create our first lines of code.

As we agreed in the [previous post](http://http://olid16.github.io/layered-architecture), our acceptance tests will live at interaction level, being an interaction the guy responsible of pulling the domain together to accomplish a user story. In the past I've thought in acceptance testing as the entry point of our system, but if we're going to follow that approach a big chunk of system will remain untested. What that means is that we should still unit tests our delivery layer, our client and we should keep an small suite of end to end tests for most critical paths.

If you search on google Testing Pyramid, you will have an idea of how little consensus are about what and how we should be testing.

![image](https://31.media.tumblr.com/a7eefe2303840e0bfec35641f3c4c955/tumblr_inline_ngq4kk4EPO1qb5vo1.jpg)

At least everybody agrees in the pyramid shape; we should keep our slow test suite as small as possible, since a flaky or slow continuous integration environment is almost worse than no having tests at all. If you have slow feedback loops or you don't trust them, it won't add any value and it will become a hassle for everyone.

So if you have clear how your system is going to look like, e.g Angular frontend, dropwizard middleware serving json and postgres db, you should start with an end to end that exercises your whole system.

If [as me](http://olid16.github.io/2014/12/13/in-the-quest-of-a-significant-pet-project/), what you have is just a business specification, you could start writing an acceptance test. Ignoring acceptance vs e2e topic, you will follow this loop anyway:

![image](https://31.media.tumblr.com/b6733654149052bb6e97b03e2424b2d2/tumblr_inline_ngq4yobNHJ1qb5vo1.jpg)

For acceptance testing I would recommend using Cucumber JVM. It has a great community behind and the tooling in JVM world is quite awesome. You don't have to worry about writing regular expressions, as there are plugins, like the Intellij one, that will generate the glue java code for you.

Once you have that you should start writing the components that will make your acceptance test green. But, obviously, writing the tests first. If you should follow inside-out or the opposite, mocking vs classic or state vs behaviour, is a complex topic. We'll talk about that in the future.