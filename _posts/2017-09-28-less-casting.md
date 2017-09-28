---
layout: post
title:  Less casting in JavaParser
date:   2017-09-28 16:16:01 +0100
comments: true
tags: javaparser
---
## Less casting in JavaParser
A common pattern in code using JavaParser looks as follows:
```java
// We get a field that has a pretty wide type: Expression
Expression condExpr = ifStmt.getCondition();
// We want to know if it happens to be a BinaryExpr, so instanceof
if (condExpr instanceof BinaryExpr) {
    // Yeah, we already know it is a BinaryExpr, 
    // but Java wants you to cast it anyway... 
    BinaryExpr cond = (BinaryExpr) condExpr;
    // Now we can use cond...
}
```

Federico Tomassetti, who maintains Java Symbol Solver,
[suggested we move some helpful methods from that project to JavaParser](https://github.com/javaparser/javaparser/issues/1154)
so that we can do this:

```java
Expression condExpr = ifStmt.getCondition();
// Use a special method to avoid instanceof
if (condExpr.isBinaryExpr()) {
    // Use a special method to avoid the cast
    BinaryExpr cond = condExpr.asBinaryExpr();
    // Now we can use cond...
}
``` 

Not only is this much more pleasing to the eye,
your IDE will suggest these methods for you,
so you can pick the correct sub-node without having to go through the class hierarchy.

I thought we could use Java 8 to improve it even more:

```java
ifStmt.getCondition().ifBinaryExpr(cond ->
    // Now we can use cond...
);
```

`cond` has the correct type and is ready to be used right away :-)

These methods are available at most nodes.
I did not implement them on `Node` or other very wide types because the amount of methods would explode,
and that would not be helpful to anyone. (`statement.isExpression()`?!)

Have fun cleaning up your code!
