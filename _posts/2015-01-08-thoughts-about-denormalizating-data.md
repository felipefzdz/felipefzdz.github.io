---
layout: post
title: "Thoughts about denormalizating data"
description: ""
category: 
tags: []
---
{% include JB/setup %}

If you read my [previous post](http://olid16.github.io/2014/12/18/defining-your-data-storage/) you'll know that I decided to design my system as microservices in order to think in boundaries clearly. I don't suggest starting to over-engineer your project at such an early stage, but if your intent is learning, as is mine, then that architecture is great.

Thanks to that, I have to think deeply in communication, as now I can't use simple java method calls, but I need to use Http. I decided to use Spark as server rest library (awesome support for routing, but it needs Java 8) and Retrofit and client rest library. I'm storing my data in MongoDb, so I chose Json as data format. One of the improvement areas of my app is defining clearly when I need to convert json to Java objects and where to do it. For now I'm using the Mongo Java Driver directly, so I'm not annotating my domain classes with any Jackson annotations. That means that I got some low level adapters for that, and the responsibilites for parsing json requests into semantic data into some ambiguous classes. Something to improve as I said. 

Another stuff that you have to think is about normalization and denomarlization data. In a monolothic app doing joins, adding indexes, changing the schema is extremely easy (at least from a development point of view), but in a microservice system, the communication makes everything more complex. So let's say that I got this scenario: As a jobseekers I want to see the jobs that I saved previously. In this context I've got two different services, one for users and one for jobs. 

* A user has the following data: id, name and role
* A job has the following data: id, employer_id, title and jobseeker_ids

The decision about storing jobseeker_ids into job collection is just temporal, so don't get attach to that. I can imagine a UI like this, for this scenario:

**My Saved Jobs**

* Programmer in Cat Ltd
* QA in Dog Ltd

At that moment, the user should be logged in, so we'll have available the jobseeker_id. The client can do a rest call to job service like /jobs/:jobseeker_id. That json will contain a list with the titles and employer_ids among other things. But what we need is the employer name, not the id. So we'll need to do another rest call, this time to user service, to /users/:employerId.

We can easily get how expensive is doing that, so many network calls! But we can't then jump into general conclusions. That we'll depende on our infrastructure and the data volume. The important thing is to realise that in normalised database (aka not duplication, every data lives in its table or document) doing joins are something like those network calls. Obviously there will be cheaper, but not for free.

There are two strategies used by databases to lighten that reading problem. One it's using indexes that in the end are copies of some data that we'll use to query our tables. Most dbs add automatically an index when you define a field as primary key, as it's a likely candidate to be queried by. The other strategy is denormalising your schema. That means copying data redundantly into different tables. In our scenario, we would add employer_name field into job collection.

Denormalising has its own caveats though. The obvious one is keeping everything consistent. Let's say that one of the employers decide to change its name from Dog Ltd to Super Dog Ltd. If we'd our data normalised we shouldn't have done anything, but as now the data lives redundantly in two services, we need to communicate that. If you use some kind of event based framework as RabbitMQ, to publish and consume your domain events (as I got planned), that we'll be managed there. Obviously, you're adding complexity to your system, but if the performance degrades enough you'll have to take decisions like that often. As well you'll face the challenges of eventual consistency.

If we keep thinking in tuples [like here](http://olid16.github.io/2015/01/01/freedom-vs-power/) we could see denormalising vs normalising as read vs write. A denormalised data model will be better for a heavy reading system, but it will face more problems when writing, as some communication will be in place.

