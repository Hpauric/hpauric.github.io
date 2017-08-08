---
published: true
title: What Exactly Is JSX?
---

## Curly Brackets

In javascript, curly brackets normally represent a function or an encapsulated piece of code that needs to be executed as one. (They can also be used to create an object literals.)
React uses curly brackets extensively in the same manner, but in a markup context.

### Commenting

> You can use regular `/* Block Comments */`, but they need to be wrapped in curly braces:

> `{/* A JSX comment */}`

### Functions and Expressions

You can place a function inside curly brackets with React. You can actually embed **any JavaScript expression** in JSX by wrapping it in curly braces.

```javascript
 {
    [1, 2, 3, 4, 5, 6, 7, 8, 9].map((val, i) => {
        return <Keypad 
                clickHandle={this.keypadClick.bind(this)}
                buttonText={val} />
     })
 }
```

JSX  will never be part of the official Javascript canon. [Quote the website](https://facebook.github.io/jsx/):

> **It's NOT a proposal to incorporate JSX into the ECMAScript spec itself**. It's intended to be used by various preprocessors (transpilers) to transform these tokens into standard ECMAScript.

> Why not Template Literals? 
ECMAScript 6th Edition (ECMA-262) introduces template literals which are intended to be used for embedding DSL in ECMAScript. Why not just use that instead of inventing a syntax that's not part of ECMAScript?
Template literals work well for long embedded DSLs. **Unfortunately the syntax noise is substantial** when you exit in and out of embedded arbitrary ECMAScript expressions with identifiers in scope.
