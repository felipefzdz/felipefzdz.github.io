---
layout: post
title: "Unit testing mess"
description: ""
category: 
tags: [tdd, unit testing, OOP]
---
{% include JB/setup %}

When you're new in a knowledge field is a good advice trying to follow what the domain experts has to say.
With time and practice you can distill your own opinions, but it usually takes time. With unit testing there are so many 
contradictory advices coming from different experts, so it's quite hard to know what to do. If you have followed the 
TDD is dead affaire you know what I'm talking about. So, ok, we agree that we need to test our code, and maybe, we even
agree that tests should be written first, but then, there are so many possible bifurcations. Working effectively with unit 
testing is fantastic book that tries to address that mess. Let's enumerate some of my past doubts about unit testing.

1. Isolation Scope: when we practice unit testing, we don't want to exercise the whole system. The word unit means some
piece of your system with clear boundaries. Assuming that you're using an OOP language, classes seems a natural boundary. 
That means that we should provide doubles for our collaborators, so we just test what is in our unit. However that's just
one of the options. You can think in units