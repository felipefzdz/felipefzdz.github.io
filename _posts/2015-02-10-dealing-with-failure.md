---
layout: post
title: "Dealing with failure"
description: ""
category: 
tags: [oop, architecture, functional programming]
---
{% include JB/setup %}

This is a complex topic with so many confusing ideas around. If I say some words, we'll maybe struggle just agreeing in the meaning of those: exception, error, failure, checked, unchecked, recovery, control flow and so on.

Let's try to agree then in at least some dichotomies:

1. Sunny day path vs whatever you call it: every method, class or system has a purpose. We can define the sunny day path when given some state the system is able to fulfil that purpose.
2. Expected vs unexpected: we say that some failure is expected by our system if we could think on it upfront and we took some measures in our code to handle it.
3. Recoverable vs mayhem: an issue is recoverable if someone (being a computer or human) can react to that failure in order to achieve the system goal.

When I saw this [great speech](http://typesafe.com/resources/video/failure-the-good-parts) I realised that there was another dichotomy pretty useful to understand this topic: Failure vs Validation. It's important to note that I didn't start yet to speak on how we're going to handle those conditions, but let's write some examples to clarify.

1. The input for my function doesn't hold some class/method invariants, so we would like to inform to the caller that the action is impossible to be performed with that input.
2. Some dependency gets unresponsive so we would like to inform someone of that problem. That guy should have enough information to react properly, e.g retrying n times or notifying somebody. It's important to note that that responsability doesn't feel right on the callee, but maybe its place is not on the caller either.
3. There is some bug on the code, so nobody can fix that immediately. Even that, we don't want to crash the whole system or showing some nasty stacktrace to user. So we would need some strategy for that.

We can see that those three categories are quite different, but however in Java world, they're usually managed with the same construct: exceptions. We all know that there are two types of exceptions in Java: checked and unchecked, but I think that we agree that the sense of those has became a little blurry. Checked should force to the caller to handle that guy, but reality is that blindly re-throwing exceptions or swallowing them are more common than we'd like. My question here is, do we really want to use expections at all and if not, is there any other alternative?

The first idea comes from Scala. [This talk](https://skillsmatter.com/skillscasts/5834-effective-api-design-with-scala-types) was amazing and I recommend to have a look on it to understand properly the concept of Either in Scala. The idea is pretty much returning an Either type that has two implementations: Left and Right. By convention Left is the error holder and Right the success one. For the impatients, read [this post](http://alvinalexander.com/scala/scala-either-left-right-example-option-some-none-null). 

I'm reading [Functional programming in Scala book](http://www.manning.com/bjarnason/) and there it shows this idea. I agree completely that throwing an exception breaks the sense of a pure function, since now that function has two possible outcomes. I don't remember where I read recently this, but shows the point: Throwing exceptions is like using gotos, but you don't know where it goes.

That's important because it makes harder to reason about the behaviour of the function, and everybody agrees that simplicity is a good value in software. That solution is similar to the solution proposed in that book to handle state. Let's say that you have a counter field in an object. Instead of keeping that state there, with the related hassles, we could return that state with a wrapper type that holds the computation result and the state. Now we'll have a different set of problems that I didn't explore yet, but I can understand the benefit.

Second idea cames from Scala world as well, this time from Akka framework. As Victor Klang stated before, validation and failures are different and we shouldn't complect them. We could use Either approach for validation errors and supervisor strategies for failures. Before of getting into supervisors, let's note (for Java8 or Guava users) that Either approach is similar to Option or Optional. Instead of returning null, we return a parameterized type that can be easily chained. That's what is called a monad, but, well, let's talk about in a future post.

Coming back to supervisor idea, I said before that the failure reaction shouldn't be accomplished by neither caller nor callee. In Akka you define supervisors that, with different roles, can handle different failures. It's not the same having some unresponsive network call than a disk failure, and our recovery/notification strategies should reflect that.

A lot of talk but, what is my current strategy when writing java apps? To be honest, I'm still exploring it. I'm using Hystrix for circuit breaking some failures when communicating with external processes. I'm still using exceptions for validations (pretty much unchecked). What is true is that is an area of my coding practices that I'm not completely happy, so I'll probably change in near future.     

