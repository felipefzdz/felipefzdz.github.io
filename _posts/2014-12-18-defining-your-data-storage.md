---
layout: post
title: "Defining your data storage"
description: ""
category: 
tags: [ddd, data modeling, microservices, architecture]
---
{% include JB/setup %}

[My spec](https://github.com/TheLadders/object-calisthenics#exercise) has 5 clear entities. The relationships between them are:

1.  An employer can post n jobs
2.  A jobseeker can save n jobs for later viewing
3.  A jobseeker can create n job applications
4.  A job application has one job related
5.  A job application may have one resume attached

The features of the system are creating those entities under some rules and some kind of reporting.

When defining some relational data schema you usually sketch some boxes with your entities and the relationships between them. If you get any n to n relationship, you create another table and if you have inheritance you scratch your mind as you'll have several options.

If your schema is complex and ORM comes handy to transition from relational to objects. However there are some accusations against those framework about being slow, hard to understand and rigid. My main intent with this project is understanding stuff clearly, so I will discard using an ORM.

I will use then some document database to store my data and I will split them in different services. As a first idea, the bounded contexts that I'm going to use are:&nbsp;User Service,&nbsp;Job Service and&nbsp;Job Application Service

*User Service

1.  CRUD employer
2.  CRUD jobseeker
3.  CRUD resume

*Job Service

1.  CRUD job
2.  Save jobs for later viewing
3.  Get jobs by employee
4.  Get saved jobs

*Job Application Service

1.  CRUD job application
2.  Reporting

Splitting services will allow me to practise some ideas behind microservices as circuit-breakers or consumer-driver contracts, but as well it'll me think more deeply about how to store my data. When you fetch a related entity with Hibernate feels really easy, maybe too easy if we're worried about proper design and performance.

If you have to do a rest call through network to get an employer entity from job application service, you will think two times if storing an id in job application document is the best approach, or maybe we should denormalize that data. You should have that line of thought with a relational database and a monolith middleware anyway, but microservices (or modular systems, if you want) encourages that for free.&nbsp;