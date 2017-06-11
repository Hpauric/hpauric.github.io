---
layout: post
title: React Notes
published: true
---

## Creating Components

There are two ways to create a React Component:
### 1. Using the class constructor

```javascript
class componentOne extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <h1>Hello World!</h1>
    );
  }
}
```

### 2. Declaring a variable 

```javascript
const componentTwo = function() {
  return (
   <h1>Hello World!</h1>
  );
};
```
## Using Props

Props are just the way to identify the html component attributes.

Say I render the `componentOne` class I created earlier as follows:

```javascript
<button id={this.props.type} 
	onClick={this.manageEvent.bind(this)}>
{this.props.buttonText}
</button>
  ```
  
  When I am using the component, I can pass values to the props by assigning them in the component declaration:
 
 ```javascript
        <componentOne
        type='clear' 
        clickHandle={this.displayClear.bind(this)} 
        buttonText={'Clear'} />
          ```


## Handling Events

> When you define a component using an ES6 class, a common pattern is for an event handler to be a method on the class.

We need a method that takes an event as its argument in the `Keypad` class. Let's call this the `EventFunctionManager`.

Then we use this function in the element:

`<button onClick={this.eventFunctionManager.bind(this)}`

## Event Bubbling

There’s a lot of different ways to manage event bubbling through nested components.

The `Keypad` is passed in the numberkey in the `Main` class render function

`Keypad` component render:

`{this.props.buttonKey}`

`Main` component render:

`buttonKey={val}`

The container div has the id `“keypad-container”`

Declaring the keypad containers in the render function in the main component

`<Keypad clickHandle={this.numberClick.bind(this)} />`

The `Keypad` and `Main` components both have event functions called `numberClick`. Is this necessary?

Further reading on event bubbling:

http://stackoverflow.com/questions/32560744/react-event-bubbling-through-nested-components

http://stackoverflow.com/questions/30477042/react-js-how-to-bind-passed-in-event-handlers-this-to-child-component

### Binding

Why do we need this?

```javascript
this.handleClick = this.handleClick.bind(this);
```

> This is not React-specific behavior; it is a part of how functions work in JavaScript. Generally, if you refer to a method without `()` after it, such as `onClick={this.handleClick}`, you should bind that method.

```javascript
(function(){
    this.handleClick();
});
```
is the same as:
```javascript
(this.handleClick.bind(this));
```

## Curly Brackets

In javascript curly brackets normally represent a function or an encapsulated piece of code that needs to be executed as one. They can also be used to create an object literal.
React uses curly brackets extensively.

### Commenting

> You can use regular `/* Block Comments */`, but they need to be wrapped in curly braces:

> `{/* A JSX comment */}`

### Functions and Expressions

You can place a function inside curly brackets with React. I.e., you can embed any JavaScript expression in JSX by wrapping it in curly braces.

```javascript
 {
    [1, 2, 3, 4, 5, 6, 7, 8, 9].map((val, i) => {
        return <Keypad 
                clickHandle={this.keypadClick.bind(this)}
                buttonText={val} />
     })
 }
```
