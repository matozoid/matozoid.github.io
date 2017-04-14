---
layout: post
title:  "Semantic validations in JavaParser"
date:   2017-11-17 16:16:01 +0100
categories: javaparser
---
As I was learning how JavaParser worked by fixing bugs and doing small changes,
I kept noticing comments in the grammar file saying "this should be checked semantically."

At first I had no idea what this meant.
When the quarter finally dropped,
it caused a bunch of new ideas.

What it means is that we don't want the grammar to specify the exact Java grammar.
It should parse all possible Java files,
but it also parses a lot of faulty Java files.
There are a few reasons for this:
- it simplifies the grammar. Rules can be reused (check out the `Modifiers()` rule which is used *everywhere*)
- it makes error recovery less urgent. The parser happily continues with some types of syntax errors.
- it makes versioning possible without having multiple grammars. The parser parses any version of Java.
- it makes the parser faster. We need less rules, we do less backtracking, etc.

But!
If we do this,
how can our users trust the AST?
It can contain nonsense!
For example:
- a class that extends multiple classes is fine
- an interface can be abstract
- a try statement can look like this: `try{}`

To restore faith in JavaParser,
we have added *semantic validations* in version 3.1.2.
These are validation that happen *after* parsing.
At this moment we have an AST that may contain nonsense,
but the validations will comb through the AST to find any kind of inconsistencies and report those as `Problem`s.

A validator is nothing more than a simple function:
```java
public interface TypedValidator<N extends Node> extends BiConsumer<N, ProblemReporter> {
    void accept(N node, ProblemReporter problemReporter);
}
```
It is a pretty standard pattern for validators,
you can find [a variation of it in Spring](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/validation.html), for example.
The whole idea is that JavaParser,
after doing the parsing work,
passes the resulting node to a validator.
This validator inspects the node (and/or its children) in some way and reports any trouble with the `problemReporter`.
Some validators are composed of other validators - `Java8Validator` is a pretty complex thing,
and some are very simple - `Java1_0Validator.noModules` simply checks if any node in the AST is a `ModuleDeclaration`.

As a user, you can make custom validators, anything really,
as long as it is implements that `accept` method.
  
The validators that JavaParser runs are configured in `ParserConfiguration.validator`.
Since it can only be a single validator,
it makes sense to make that a validator that is composed of other validators.
[This post shows how to change this validator.]({{ site.baseurl }}{% post_url 2017-04-14-enable-java-9-support %})

You can also disable validations completely to save some CPU cycles:
```java
JavaParser javaParser = new JavaParser(new ParserConfiguration().setValidator(new NoProblemsValidator()));
```

As a final note: the output of the validators may look a lot like the output of the Java compiler,
but it is not meant to become that powerful.
On one hand, because it is an absurd amount of work to get this exactly right,
on the other hand because a parser just doesn't have that much information.
We would at least need to [solve symbols](https://github.com/javaparser/javasymbolsolver), but that's not that task of the base library.