---
layout: post
title:  "Enabling Java 9 support"
date:   2017-11-17 16:16:01 +0100
categories: javaparser
---
When support for parsing Java 9 files was added in version 3.1.4,
we didn't turn it on by default.
We set the default language level to 8,
with the idea to set it to 9 around the time JDK 9 comes out.

So how do you change the language level?
By changing the validations done to the AST.
Where are these validations set?
In `ParserConfiguration`.

```java
Java9Validator java9Validator = new Java9Validator();
ParserConfiguration parserConfiguration = new ParserConfiguration().setValidator(java9Validator);
JavaParser javaParser = new JavaParser(parserConfiguration);
```

```java
JavaParser javaParser = new JavaParser(new ParserConfiguration().setValidator(new Java9Validator()));
```

This works for every major Java version.

```java
// Welcome to the nineties!
JavaParser javaParser = new JavaParser(new ParserConfiguration().setValidator(new Java1_0Validator()));
```