---
layout: post
title: What is an Immediately Invoked Function Expression?
published: true
---

To answer this question, it's best to take a step back and look at the concepts behind this term. First of all, what exactly is an expression? An expression is any section of code that resolves to a value. So what exactly is a function expression?

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

## Immediately Invoked Function Expressions (IFFEs)

So what is a function expression that's immediately invoked? Exactly what it sounds like. It's a function expression that is run (invoked) as soon as the line is reached during execution.
How do you do this? You can invoke any function by putting parens after it (`()`).

However, you also need to wrap the function in parens, in order to avoid a syntax error. This tells the parser to expect a function expression, and not a function declaration. This is because *parens can't contain statements*.

```javascript
(var x = 5) // Not allowed!
```
So we can immediately invoke our function expression from earlier by wrapping it in parens, and appending parens to it. And now we no longer have to assign a variable name to it!
```javascript
/*const functionOne = */(function() {console.log("Hello!"); })();
```
So now that we know what IIFEs are, all we have to do is find a reason to use them! IIFEs are useful when you just want to run a function once, and you don't need to return a value.
