---
layout: post
title: React Notes
published: true
---

## Creating Components

There are two ways to create a React Component:
1. Using the class constructor
2. Declaring a variable (const)

## Handling Events

> When you define a component using an ES6 class, a common pattern is for an event handler to be a method on the class.

We need a method that takes an event as its argument in the `Keypad` class. Let's call this the `EventFunctionManager`.

Then we use this function in the element:
`<button onClick={this.eventFunctionManager.bind(this)}`


## Curly Brackets

### Commenting

> You can use regular `/* Block Comments */`, but they need to be wrapped in curly braces:

> `{/* A JSX comment */}`


You can throw a function inside the curly brackets.

I.e., you can embed any JavaScript expression in JSX by wrapping it in curly braces.

```javascript
 {
    [1, 2, 3, 4, 5, 6, 7, 8, 9].map((val, i) => {
        return <Keypad 
                clickHandle={this.keypadClick.bind(this)}
                buttonText={val} />
     })
 }
```





