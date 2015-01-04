---
layout: post
title: "Brief Java 8 streams tour through an example"
description: ""
category: 
tags: []
---
{% include JB/setup %}

The code and spec for this project can be found here.

Let's start talking about the parser. One of this project intents is using Java 8 lambdas to solve problems in collections views as streams. We could think about a file separated by lines as a stream of strings. So we'll use lines method from Buffered reader class to create a stream.

    public ParsingResult parse(String path) {
        try (BufferedReader reader = createReader(path)){
            List<String> header = parseHeader(reader);
            return parseContents(reader, header);
        } catch (IOException e) {
            throw new ParsingException(e);
        }
    }

    private BufferedReader createReader(String path) throws IOException {
        return Files.newBufferedReader(Paths.get(path));
    }

    /**
     * Parse header and consume the first line of the reader.
     */
    private List<String> parseHeader(BufferedReader reader) {
        return reader.lines()
                .findFirst()
                .map(Strings::splitByComma)
                .orElseThrow(ParsingException::new);
    }
    
It's important to note that stream collectors like findFirst actually consumes the items from the stream, so if we want to access to the next lines of the stream we could pass the original stream, instead of doing some kind of skip(1).

FindFirst method returns an Optinal<T> type. If you're used Guava Optional you'll be familiar with it. The one provided by the JDK has support for high-order functions so it integrates seamlessly with streams api.

Something interesting that we can see in this code is the use of method references. That is just syntatic sugar for lambda expressions. Compare them:

                .map(Strings::splitByComma)
                .map(header -> Strings.splitByComma(header))
              
Now we'll see one of the problems with Java8 streams. Our parser tries to be as general as possible. In order to accomplish that we'll use the following Parsing Types:

* Entry. This type is just a wrapper for a key/value pair. Here I'm missing some built-in types in languages like Scala. The key will be the corresponding header and the value the content in the file. That means the the semantic association in our csv files is hold by index. 
* CsvEntity. This entity holds a list of entries, that following some business rules, can generate a domain entity.

With those rules on mind, we need some construct that allows us to mix header and content lines into vertical slices or Entries. In Scala there are some high-order functions like zip, but java 8 api is missing that one. In order to keep and use the index of the collection in which we base our stream we need to write this kind of not awesome code:

    private CsvEntity csvEntity(List<String> header, List<String> content) {
        if (header.size() != content.size()){
            throw new ParsingException();
        }

        List<CsvEntity.Entry> entries = IntStream.range(0, header.size())
                .mapToObj(i -> new CsvEntity.Entry(header.get(i), content.get(i)))
                .collect(toList());

        return new CsvEntity(entries);
    }
    
Now let's have a look into the reporter. This project follows some of the DDD ideas, e.g primitives aversion (wrapping primitives into value types, creating a much more rich domain) or avoiding anemic domains (extracting behaviour from procedural services into their legit owners in an OOP world, the domain entities)

Our domain is quite simple, seniors and interns. There are rules that apply to some single entity or to the collection of them, so will have different classes for Senior/Seniors and Intern/Interns. On that way, the reporter is the application service, actor, interactor or just the procedural guy that simply calls the proper behaviour contained into the domain.

Manipulating collections is when java 8 streams really shines. Try to think how complex and bug-prone would be an imperative version of this code:

	public double averageAge() {
        return seniors.stream()
                .mapToInt(senior -> senior.age().value())
                .average()
                .orElse(0);
    }

Being seniors a Collection<Senior>. MapToInt method returns an IntStream that has the most typical aggregation operations as sum, max or min. Those guys returns an Optional type that allows you easily to provide a default value, if the collection is empty, using `orElse(0)`.

A simple example with filter method doesn't need explanation:

    public long countBy(Sex sex) {
        return seniors.stream()
                .filter(senior -> sex.equals(senior.sex()))
                .count();
    }
    
Hopefully with this project I'll convince to use Java 8 streams to anyone that thinks that functional programming is for super clever rock-stars. I mean, yes, functional programming can be quite complex, but you don't need to understand and use every bit of the functional paradigm every time.





 
