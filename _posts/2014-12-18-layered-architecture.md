---
layout: post
title: "Layered architecture"
description: ""
category: 
tags: []
---
{% include JB/setup %}

As long as an architecture has some minimal structure, it will have layers. What I'm going to talk today is about some kind of layered architecture quite typical in java projects.

This architecture follows this flow:

UI -&gt; Controller -&gt; Facade -&gt; Service -&gt; Dao -&gt; DB

UI &lt;- Controller &lt;- Facade &lt;- Service &lt;- Dao &lt;- DB

This structure suggests several thoughts:

*   The communication always starts and ends on UI
*   Our middleware just talks with a DB. Of course we can always inject a component that sends message to another rest endpoint, jms, actor... Also DAO (data access object) is about data, not DB, and we should design our system as modular as possible, but sometimes the concept of DAO layer being a DB wrapper pervades in our mind.
*   The flow will be synchronous. This is not only an idea, but a technical issue. Classical EE specs as old servlets or jdbc are blocking, so they're not asynchronous. [This post](http://www.ratpack.io/manual/current/async.html) is brilliant to understand the differences.
*   Naming is quite unclear. We know that controller layer will be in charge of managing rest endpoints, but we know it because it's a convention, not because the name leads us to that.
*   The business logic will live mainly in services, creating anemic domains and not really OOP code. Think in services as procedurals holders of behaviour that interact with POJOs as data wrappers. No OOP at all.

`if(bookService.isAvailable(book))`

vs

`if(book.isAvailable())`

*   Where should start acceptance testing? From UI, Controller, Facade? If we follow the advice of Uncle Bob about starting in the interaction, and not in the UI or Rest, that means Facade in our architecture. But clearly Facade is not a name the implies that.

There are other architectures that address these concerns like Hexagonal Architecture aka Ports and Adapters, DDD architectures, Clean Architecture and so on. I'm going to try to extract some common ideas from them:

*   Your domain is your core. Structure your business logic around concepts that your domain expert or business analyst uses. Entities, Collections or Aggregates should be the guys keeping the behaviour and some internal state. Take advantage of the OOP constructs to create simple and clean domains.
*   We create a domain as means to an end, that is serving business value. That value is stated as user stories, interactions, actions, examples... We should keep that interaction in its own layer, making really to clear to a new developer what does our system. This layer puts in motion the domain to accomplish an objective, so it's a great place to write our Acceptance tests.
*   Now we can integrate our core system into different systems and transport mechanism easily and without fear of breaking our business core, as it's isolated and covered with our test suite. Our old controller, or web layer, could now be thought as a delivery layer, so it will be really easy for use changing that from returning html to pure json, for instance.
*   The domain will send messages (calling methods in OOP has the same semantic as sending messages) to another systems, being DBs, queues, webservices and so on. That package should be isolated into an infrastructure module, and because its slowness and lack of control, will be a candidate to be mocked in most of our tests.
*   As our system is designed with integration in mind, it's easier for us calling more and more external apis, to accomplish our user stories. That means that our code should enforce async communications as much as possible.&nbsp;

Reading DDD books is a good place to start to grasp those ideas. As it could be quite overwhelming at first (so many dense concepts), my advice is having a look at this [google group](https://groups.google.com/forum/#!forum/clean-code-discussion)&nbsp;about Clean Architecture.

If you want to invest some money you can even purchase [Uncle Bob screencasts](http://cleancoders.com/) that are discussed in that forum.