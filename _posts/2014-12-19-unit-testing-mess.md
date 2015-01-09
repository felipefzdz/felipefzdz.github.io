---
layout: post
title: "Unit testing mess"
description: ""
category: 
tags: [tdd, unit testing, oop, testing]
---
{% include JB/setup %}

When you're new in a field a good advice is trying to follow what the domain experts has to say.
With time and practice you can distill your own opinions, but that usually takes time.  There are so many 
contradictory advices coming from different experts in unit testing world, so it's quite hard to know what to do. If you have followed the 
[TDD is dead affaire](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html) you know what I'm talking about. So, ok, let's say that we agree we need to test our code, and maybe, we even
agree that tests should be written first, but then, there are so many possible bifurcations. [Working effectively with unit 
testing](https://leanpub.com/wewut) (wewut) is a fantastic book that tries to address that mess. Let's enumerate some of my past doubts about unit testing.

1. Isolation Scope: when we practice unit testing, we don't want to exercise the whole system. The word unit means some
piece of your system with clear boundaries. Assuming that you're using an OOP language, classes seems a natural boundary. 
That means that we should provide doubles for our collaborators, so we just test what is in our unit. However we could think in unit as a couple of classes working together. Wewut proposes as vocabulary Solitary unit test vs Sociable unit test.

2. Status vs behaviour: or classicists vs mockists. That depends on what we want to test. This issue leads us to think in the nature of our OOP code. What is this method doing? Is it updating some internal state? Is it transformating some data? Is it just sending some messaged to its collaborators? Depending of what our code does we'll choose the best approach for testing.

3. Mocks, stubs and stuff: when you start with unit testing, the differences between double types could be blurry. Using a library called Mockito to produce stubs could increase that confussion. Let's define briefly the most populars doubles:

	* Stub: double that returns what we want. E.g	 `when(myCollab).run().thenReturn(aValue)`
	* Mock: double that records its interactions. Used to behaviour unit testing
	 `verify(myCollab, times(2)).run()`
	* Spy: real collaborator with recording capabilities. Handy if we want to do behaviour sociable unit testing

4. Outside-in vs Inside-out testing: when we start building a layered architecture, we have to made a decision about starting from the leafs or from the root of that graph. Both TDD approaches imply some design beforehand, but in practice the challenges are different. My preference is a mix of both styles. I start TDDing the domain with outside-in and then the adapters with inside-out.  
	
These questions appear when testing but naturally leads us to think in our production code. Expanding a little what I stated in Status vs Behaviour an OOP method can do the following:

* Returning a value
* Maintaining some internal state
* Sending messages to its collaborators
* Using messages from its collaborators

A method that just delegate or forward messages into its dependencies, could be useful or a smell depending on the context. That kind of procedural style is legit in the actions but it's a smell of poor OOP design when found it in domain code.

Wewut book also provides some guidelines to write sane test code. Guidelines as one assertion per test or using data builders could serve us figuring out why that dependency is so hard to build or why that method requires so many tests.

 	