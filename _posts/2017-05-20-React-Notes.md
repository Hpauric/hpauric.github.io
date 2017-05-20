---
layout: post
title: React Notes
published: true
---

## Creating Components

There are two ways to create a React Component:
1. Using the class constructor
2. Declaring a variable (const)

> When you define a component using an ES6 class, a common pattern is for an event handler to be a method on the class.

We need a method that takes an event as its argument in the `Keypad` class. Let's call this the `EventFunctionManager`.

Then we use this function in the element:
`<button onClick={this.eventFunctionManager.bind(this)}`
