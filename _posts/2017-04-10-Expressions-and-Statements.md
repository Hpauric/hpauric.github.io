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

Because of hoisting, I can use this function before it is declared:

```javascript
functionTwo(); // Hello!
function functionTwo() { console.log("Hello!"); }
```



## Immediately Invoked Function Expression

IIFEs are useful when you just want to run a function once.

You can invoke any function by putting parens after it (`()`).

However, you also need to wrap the function in parens, in order to avoid a syntax error. This tells the parser to expect a function expression, and note a fucntion declaration. This is because parens can't contain statements.
