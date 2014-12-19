---
layout: post
title: "Structuring your code"
description: ""
category: 
tags: [architecture, craftmanship, ddd]
---
{% include JB/setup %}

Once you've defined what is the best language, platform, framework, libraries and so on, you need to decide how you're going to write your code. What I'm going to say is valid to most domains, but let's focus in a middleware with two ports: rest api and a DB.

Old approaches could suggest you to write software in horizontal slices. You could write first your persistence layer, you could even assign that task to a team expert in ORMs and DBs, and then write the other layers. This approach goes against Agile principles, and nowadays is not so popular. But even that we still structure our code in technical layers.

In [this post](http://blog.8thlight.com/uncle-bob/2011/09/30/Screaming-Architecture.html), Uncle Bob notes that most projects,&nbsp;<span>when you have a quick overview on them</span>, tell you about the technical theme but not about the domain. You can easily spot that the project is following MVC, that is using RoR or whatsoever, but, if you want to know if it's a financial, health or news application, you'll need to dig into the details.

Lately trends in programming try to overcome some obscurity not inherent to programming. Why don't we write our software as it were intended to be read by business guys? I'm not only talking about BDD, aka specification by example, but about using an [Ubiquitous Language](http://martinfowler.com/bliki/UbiquitousLanguage.html)&nbsp;when naming variables, methods, classes, packages or services.&nbsp;

When I explain those concepts to a newbie I always say the same, you should write your code as a child or a business guy is going to read it in the future. Pun intended ;) But the point is that we should try programmers as children when they're going to read your code. I don't want to spend &nbsp;cognitive effort understanding code that should have been written using a shared vocabulary first.

And by the way, the communication with business will be a pleasure if you follow this advice. Let's say that you implement a web crawler and you decide in your code talking about URLs, put your business analyst always talks about Pages, but you don't like that name and decide to promote your URL name into a more powerful Link. Sounds like a messy plan, right?

In [this talk](https://skillsmatter.com/skillscasts/5742-interaction-driven-design), Sandro Mancuso shows how convenient is choosing a domain driven approach.

![image](https://31.media.tumblr.com/b42e74d6929abc4ad4914d58b71cbb6e/tumblr_inline_ngpz0wc9ZP1qb5vo1.png)

Now, have a look into this structure:

![image](https://31.media.tumblr.com/2bba00b514622a447f7b84f6e0969eae/tumblr_inline_ngpzb4qBYV1qb5vo1.png)

![image](https://31.media.tumblr.com/ba2571264c4f12cf30f66dabeb7386ef/tumblr_inline_ngpzf4wp1x1qb5vo1.png)

![image](https://31.media.tumblr.com/6e54c7babfba9f6e43f6115b203643df/tumblr_inline_ngpzg71sN41qb5vo1.png)

I recommend watching the whole video. Sandro Mancuso is a great speaker and my next purchase will be its book [Software Craftsman Professionalism](http://www.amazon.co.uk/Software-Craftsman-Professionalism-Pragmatism-Robert/dp/0134052501/ref=sr_1_1?s=books&amp;ie=UTF8&amp;qid=1418808631&amp;sr=1-1&amp;keywords=software+craftsman+professionalism).

In my next post I'll deep into how classic layered architecture that doesn't show up domain can obscure intent, even from a technical point of view.
