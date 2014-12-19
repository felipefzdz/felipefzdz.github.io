---
layout: post
title: "How starting a new pet project"
description: ""
category: 
tags: [skeleton, archetypes, hefesto]
---
{% include JB/setup %}

Meanwhile I was reading [GOOS](http://www.growing-object-oriented-software.com/)&nbsp;I realised about something painful: start a project hurts. In your daily job, the chances are high that you got in first place into a brownfield project. Even if you're lucky enough to be assigned into a greenfield one, starting time will be an small part of project whole life.&nbsp;

There it comes frameworks to rescue. With promises of fast prototyping through convention over configuration, frameworks like Spring, Angular o Ruby on Rails will make your life easier when starting your project.

It's not my intent putting every existing framework into the same bucket, or to diminish the merits of them, but lately I'm not really keen into framework mindset.

![]({{ site.url }}/assets/libraries.png)

So, If you decide going with a libraries, more modular approach, bear in mind that the starting time curve will be high. Depending on your language/platform there are several 'accelerators' to help you:

*   <span>If you use Maven as build system you can use one the archetypes available to generate some skeleton for your project.</span>
*   <span>Through [Yeoman](http://yeoman.io/)&nbsp;generators you can easily create a prototype of angularjs, backbone or any mvc client, backed by so many apis.</span>
*   <span>[Typesafe](http://typesafe.com/get-started) activators allows you spike and explore Scala and Java technologies like Akka, Play or Spray.</span>

My experience using a yeoman generator was bittersweet. I tried to setup a Marionette/Backbone client using Grunt, Require.js, Mocha, Jasmine and so on. Javascript communities grow really fast, and it's quite easy to get into some mess with old dependencies. [Semantic versioning](http://semver.org/) seems to be a fraud in that community:

![]({{ site.url }}/assets/semver.png)

However, as discovery and learning tool, those 'accelerators' are awesome. They provide you a running app that you can inspect, debug or whatsoever without the noise and subtleties of a big project.

Why did GOOS make me think about that? Because writing first an end to end test could be damned complex if you don't really understand your system. Once @ignasi35 told me:

If you can't test that bit, means that you can't reason about it, so it shouldn't go into production

(more or less, it was an informal conversation years ago ^_^ )

To be honest, even if you understand your system, writing an end to end test could be full of surprises, so it's better if we found those as soon as possible.