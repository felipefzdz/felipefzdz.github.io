---
layout: post
title: "A tale of big refactors"
description: ""
category: 
tags: [architecture, refactoring]
---
{% include JB/setup %}

In [Hefesto](https://github.com/olid16/hefesto) I've followed some of [this architectural ideas](http://olid16.github.io/2014/12/16/layered-architecture/). The main idea is isolating the domain from the infrastructure. I need to refactor the entire rest adapter, so now I will face the truth about the modularity of my system.

I'm using [Spark](http://sparkjava.com/) for rest adapter. Why do I need to replace it?

1. I would like to use [Swagger](swagger.io) to document my APIs. As you can see [here](https://github.com/olid16/hefesto/issues/8), there is not integration yet between Spark and Swagger.
2. As I'm splitting my domain into different services, I'm starting to think in more devops concerns, as circuit breakers or service discovery. Spark is a quite lightweight library, so maybe I could use another framework with more powerful integration points.
3. From the very beginning, my intent has been being able to discard libraries easily, for its own sake. That's why I didn't have a look into Play or Spring.

I've got professional experience with Dropwizard, and I remember it with pleasure. There are so many bundles (including swagger and devops stuff) and the excitement about that framework is quite real, so let's see how easy will be replace Spark with Dropwizard. 

The first problem that I faced in the migration is the library versions collisions. Dropwizard and Spark bring with them so many libraries and some of them are incompatibles. Stacktraces use to be quite esoteric. The best approach is to use this command, mvn dependency:tree, and then see which libraries are holding different versions of the same dependency. As i'm going to get rid in a near future of spark, my temporal solution is excluding those libraries:

			<dependency>
                <groupId>com.sparkjava</groupId>
                <artifactId>spark-core</artifactId>
                <version>${spark-core.version}</version>
                <exclusions>
                    <exclusion>
                        <groupId>org.slf4j</groupId>
                        <artifactId>slf4j-simple</artifactId>
                    </exclusion>
                    <exclusion>
                        <groupId>org.eclipse.jetty</groupId>
                        <artifactId>jetty-server</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
            
The next problem was an infrastructure leak into the domain. Let's see the code for creating a user with spark:
 
	post("/user", (req, res) -> userController.create(req, res));
	
	JsonEntity jsonEntity = new JsonEntity(readFrom(req.body()));
        try {
            User user = createUser.with(jsonEntity);
            ...
            
    public User with(JsonEntity jsonEntity) {
        User user = userFactory.create(jsonEntity, users.nextId());

I thought that I was isolating the domain, wrapping the body with custom JsonEntity, but in the end, JsonEntity talks about Json. What will it happen if the adapter now receive XML over Http? Now with Dropwizard/Jersey, rest adapter looks like this:

	@POST
    public String create(User user)
    
    ....
    
    public class User {

    @JsonProperty
    private final String name;
    @JsonProperty
    private final String role;


    @JsonCreator
    public User(@JsonProperty("name") String name,
                @JsonProperty("role") String role) {
        this.name = name;
        this.role = role;
    }
}

How can I reuse the domain, if that guy is waiting for a JsonEntity and what I got is a POJO? Yes, I could convert that POJO into json and inject it into JsonEntity and so on, but that is a conversion madness. I'm going to need to change my domain to expect just domain data.

Changing the tests has been quite easy, as the only stuff changing is method signatures, but not responsabilities. [This](https://github.com/olid16/hefesto/commit/924e40b6b38a6eb0e9952459cd7ef5344cdde572?diff=split) is the commit about replacing Spark with Dropwizard in the User Service.