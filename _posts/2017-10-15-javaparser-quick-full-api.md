---
layout: post
title:  The quick and the full API of JavaParser
date:   2017-10-15 16:16:01 +0100
comments: true
tags: javaparser
---
## The quick and the full API of JavaParser

Lost in a dark corner of the JavaParser site is the idea behind the two API's of JavaParser.
Here's an attempt to put it back into the light :-)
The API I discuss here is literally the API from the `JavaParser` class.

### Quick!

This API was built to make common use cases as painless as possible.
In the end, this API is a collection of shortcuts to the full API.

```java
import static com.github.javaparser.JavaParser.*;
...
CompilationUnit cu = parse("class X{}");
```
* The quick API consists of all the static methods on the `JavaParser` class.
* All these methods throw an unchecked `ParseProblemException` if anything goes wrong.
This exception contains a list of the problem(s) encountered.
* The static methods called `parse` and `parseResource` offer various ways of reading Java code for parsing.
They all expect a full Java file.
So, even though the example parses a `String`,
you could pass an `InputStream`,
a `File`
or various other inputs.
* The remaining static methods parse a fragment of source code in a `String`.
Here is one that parses just an expression:
```java
Expression e = parseExpression("1+1");
```
* `parseJavadoc` is a special case.
Comments normally remaing unparsed, including Javadoc comments.
This method is a separate parser for a `JavadocComment` node.
* Changing configuration is done by modifying the `staticConfiguration` field.
```java
JavaParser.getStaticConfiguration().setAttributeComments(false);
```

### Full!

This API was built to give access to all flexibility there is,
and to provide a little more performance.

```java
import static com.github.javaparser.ParseStart.*;
import static com.github.javaparser.Providers.provider;
...
JavaParser javaParser = new JavaParser();
ParseResult<CompilationUnit> result = javaParser.parse(COMPILATION_UNIT, provider("class X{}"));
result.ifSuccessful(cu ->
    // use cu        
);
```
or, thanks to the fake builder pattern:
```java
import static com.github.javaparser.ParseStart.*;
import static com.github.javaparser.Providers.provider;
...
new JavaParser().parse(COMPILATION_UNIT, provider("class X{}")).ifSuccessful(cu ->
        System.out.println(cu)        
);
```
* The full API consists of the `JavaParser` constructors and the single non-static `parse` method.
* *Never* does it throw an exception.
`ParseResult` can tell you if parsing went fine, and if not what problems were encountered.
* Reusing the JavaParser instance will give a small speed boost.
* A JavaParser instance is not thread safe!
* The first parameters indicates what kind of source you will be passing.
Normally it's a compilation unit, but you can parse expressions, names, etc.
* The second parameter provides the source code.
A `Provider` is an abstraction over any kind of input.
* The full API lets you combine these parameters however you like.
* Parsing Javadoc is an exception again.
You need the `JavadocParser` for that.
* Configuration can be passed in the constructor.

```java
ParserConfiguration configuration = new ParserConfiguration();
JavaParser parser = new JavaParser(configuration);
ParseResult<Expression> parseResult = parser.parse(EXPRESSION, provider("1+1"));
if (!parseResult.isSuccessful()) {
    System.out.println(parseResult.getProblems().toString());
}
// a failed parse does not always mean there is no result.
parseResult.getResult().ifPresent(System.out::println);
if (parseResult.getCommentsCollection().isPresent()) {
    // ...
}
```
