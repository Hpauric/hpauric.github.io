---
layout: post
title: Learning How to Use React By Creating a Calculator
published: true
---


I'm going to create a calculator using React.

## Creating Components

The first thing I need to do is create components. There are two ways to create a React Component:
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

### 2. Declaring a component

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

We need a method that takes an event as its argument in the `Keypad` class. Let's call this `manageEvent`.

```javascript
manageEvent(e) {
    this.props.clickHandle(e);
  }
```

Then we use this function in the element:

```javascript
<button onClick={this.manageEvent.bind(this)}>
```

## Event Bubbling

Now we want to access this event in the `Main` component. The `Keypad` components are children of the `Main` component. We can do this by binding the event to the `Main` method we want to handle the event when we declare the component.

```javascript
<Keypad 
	clickHandle={this.keypadClick.bind(this)} 
	buttonText={val} />
```
In this case, we've bound `clickHandle` to `keypadClick`, one of `Main` component's event handling methods.

```javascript
keypadClick(e) {
    let pressedButton = e.target.innerHTML;
      if (this.state.number == "0") {
      // The default number on display is 0.
      //If 0 is present, replace it with the new number
        this.setState({								
          number: pressedButton,
        });
      } else {
      //Otherwise, append the text to the number
        let number = this.state.number + pressedButton;
        this.setState({
          number,										
        });
      }     
  }
```

If we want the Keypad event to use a different event method - for example, the clear button - we bind `clickHandle` to `displayClear` instead of `keypadClick`.

```javascript
<Keypad
        type='clear' 
        clickHandle={this.displayClear.bind(this)} 
        buttonText={'Clear'} />
```

It seems it can be even easier to pass props from child to parent.

[Passing Props to Parent Component](https://stackoverflow.com/questions/22639534/pass-props-to-parent-component-in-react-js)


Further reading on event bubbling. These may or may not be useful. A lot of people on the internet have opinions.

[React Event Bubbling Through Nested Components](http://stackoverflow.com/questions/32560744/react-event-bubbling-through-nested-components)

[How to Bind Passed-In Event Handlers](http://stackoverflow.com/questions/30477042/react-js-how-to-bind-passed-in-event-handlers-this-to-child-component)

### More Info on Binding

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
