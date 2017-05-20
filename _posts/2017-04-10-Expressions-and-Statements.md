---
layout: post
title: Expressions and Statements
published: true
---

An expression is any section of code that resolves to a value.


## Function Expression versus Function Declaration

A function **expression** declares a variable that has an anonymous function assigned to it.

```javascript
const functionOne = function() {console.log("Hello!"); }
```
A function expression is not **defined** until the line is reached during execution.

Whereas a function **declaration** (also known as a function statement) is immediately defined as soon as it’s surrounding script or function is executed due to **hoisting**.

```javascript
function functionTwo() { console.log("Hello!"); }
```
