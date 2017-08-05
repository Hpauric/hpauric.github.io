---
layout: post
title: Using Line Of Code Breakpoints with Google Dev Tools
published: true
---

Google Dev Tools are like x-ray specs for code. Nowhere is this more true than in the sources tab.

![x-ray specs]({{site.baseurl}}/images/x-ray-specs.jpg)

The Sources tab in Google Dev Tools is a great testing ground for new code. It allows you to step through code and monitor your variables and properties at each step of the execution. This is invaluable for debugging code.

To test a new code snippet, navigate to the Source tab.

Click on `+ New Snippet`

You can add whatever code you want. In this case I'm trying out a fibonacci implementation with dynamic programming. (This is taken from [a guide to implementing memoization in javascript](https://www.sitepoint.com/implementing-memoization-in-javascript/).)

```javascript
var fibonacci = (function() {
  var memo = 
  {};

  function f(n) {
    var value;

    if (n in memo) {
      value = memo[n];
    } else {
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(n - 1) + f(n - 2);

      memo[n] = value;
    }

    return value;
  }

  return f;
})();
fibonacci(10);
```
Let's say I want to see how this code works. I can set the code to pause at certain points in the execution by using breakpoints. There are several different types of breakpoints that I can use. The one I'm looking at today is the most popular: line-of-code breakpoints. I can add breakpoints by clicking on the lines of code I want to monitor:

![adding breakpoints]({{site.baseurl}}/images/adding-breakpoints.png)

These are line-of-code breakpoints. Breakpoints can only be added on certain lines of code.

Now I can run this code by clicking `Run snippet`.

I have a few options at each point it stops:

1. Resume script execution
2. Step over next function call
3. Step into next function call
4. Step out of current function

There's a detailed breakdown of how these actions differ on the [Google Dev Page](https://developers.google.com/web/tools/chrome-devtools/javascript/step-code#stepping_in_action).

The most important thing to know is: 

> Use `step into` as your typical "one line at a time" action, as it ensures that only one statement gets executed, no matter what functions you step in and out of.

`Step over` executes the next line and stops at the following line.

`Step out` completes the rest of the function and stops at the next statement.

## View and Edit Properties and Variables

The whole point of using breakpoints is that they allow you to check all of the currently-defined properties. You can even edit properties in the script on the fly.

### Scope

> The three most important aspects of debugging and real estate are the same: Location, Location, and Location.

Scope matters! The scope pane allows you to see the all variables (and functions and objects) in the local, closure and global scope.

![]({{site.baseurl}}/images/scope-pane.png)

For example, I can examine my `memo` object and check it's properties:

![memo-object.png]({{site.baseurl}}/images/memo-object.png)

It's important to note that you can only see the scope (and the call stack) when the program is paused.

### The Call Stack

I can also see the call stack on the left pane. The call stack lists all of the functions that have been called, with the most recent at the top. It might not seem that useful in this case, because all of the functions recently called have been `f`! However, I can click on these previous-called functions to see the state of the variables and objects at that time.

## Conclusion

Using breakpoints, checking the scope pane and the call stack - these are all powerful tools for debugging programs. But they are also invaluable tools to understand code you didn't write yourself!
