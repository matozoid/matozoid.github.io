---
layout: post
title:  "Enabling Java 9 support"
date:   2017-04-11 16:16:01 +0100
comments: true
tags: javaparser
---
When support for parsing Java 9 files was added in version 3.1.4,
we didn't turn it on by default.
We set the default language level to 8,
with the idea to set it to 9 around the time JDK 9 comes out.

We needed to do some refactoring for Java 10 support,
so you'll find the old way below under "pre 3.5.14"

What you want to do nowadays is to pick a postprocessor that
turns the AST into an AST for the Java version you want,
and a validator that will find problems in the AST for you.
Luckily, that's all hidden behind a simple setting in `ParserConfiguration`:

```java
JavaParser.getStaticConfiguration().setLanguageLevel(ParserConfiguration.LanguageLevel.JAVA_9);
```

Okay... Let's do an import static:

```java
import static com.github.javaparser.ParserConfiguration.LanguageLevel.*;

JavaParser.getStaticConfiguration().setLanguageLevel(JAVA_9);
```

Well, that was quick.

There are some pseudo-language levels available (from 3.5.15 on):
- POPULAR gives you the most common Java version at the time of release.
- LATEST gives you the latest stuff that JavaParser supports.
- RAW skips postprocessing and validation for a bit of a speed boost.

# Pre 3.5.14 (old stuff)

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
