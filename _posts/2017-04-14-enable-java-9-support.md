---
layout: post
title:  "Enabling Java 9 support"
date:   2017-11-17 16:16:01 +0100
tags: javaparser
---
When support for parsing Java 9 files was added in version 3.1.4,
we didn't turn it on by default.
We set the default language level to 8,
with the idea to set it to 9 around the time JDK 9 comes out.

So how do you get that delicious module support?
By changing the validations done to the AST.
Where are these validations set?
In `ParserConfiguration`.

## Easy mode

The easy API uses static methods,
so it is logical that the configuration used is static too:
 
```java
Java9Validator java9Validator = new Java9Validator();
ParserConfiguration configuration = JavaParser.getStaticConfiguration();
configuration.setValidator(java9Validator);
JavaParser.parse("module x {}");
```
or, as a one-liner:
```java
JavaParser.getStaticConfiguration().setValidator(new Java9Validator());
```
## Pro mode

The pro API uses an instance of `JavaParser`,
and the configuration goes into the constructor:

```java
Java9Validator java9Validator = new Java9Validator();
ParserConfiguration configuration = new ParserConfiguration();
configuration.setValidator(java9Validator);
JavaParser javaParser = new JavaParser(configuration);
javaParser.parse(ParseStart.COMPILATION_UNIT, Providers.provider("module x {}"));
```

or, as a one-liner:
```java
JavaParser javaParser = new JavaParser(new ParserConfiguration().setValidator(new Java9Validator()));
```

This works for every major Java version:

```java
// Welcome to the nineties!
JavaParser javaParser = new JavaParser(new ParserConfiguration().setValidator(new Java1_0Validator()));
```
