---
published: true
layout: post
title: React State Management with setState
image_url: /images/400-300px/react-set-state-400-300.png
excerpt: React's setState function is asynchronous. So don't expect for it to be invoked immediately!
---

`setState()` is **asynchronous**.
That means you have to be careful about using it. If you are updating a component in different places, and the variables are depdendant on one another, you're in for a bad time.

> Think of `setState()` as a request rather than an immediate command to update the component. For better perceived performance, React may delay it, and then update several components in a single pass. React does not guarantee that the state changes are applied immediately.

There are two ways to use `setState()`. You can pass an object to it.

```javascript
this.setState({
        number: this.state.number + 5,
        newNumberFlag: false,
      });
```

Or you can pass a function to it

```javascript
this.setState((prevState, props) => {
  return {counter: prevState.number + 5};
});
```

> `setState()` does not always immediately update the component. It may batch or defer the update until later. This makes reading this.state right after calling ``setState()`` a potential pitfall. Instead, use `componentDidUpdate` or a `setState` callback (`setState(updater, callback)`), either of which are guaranteed to fire after the update has been applied. 

You can also include a callback with `setState()`, to be implemented after it's been implemented.

```javascript
this.setState((prevState, props) => {
  return {counter: prevState.number + 5};
}, callbackFunction(){
	this.setState({
        number: this.state.number + 5,
        newNumberFlag: false,
      });
});
```
