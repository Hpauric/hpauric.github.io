
To test a new code snippet, navigate to Source

Click on `+ New Snippet`

You can add whatever code you want. In this case I'm trying out a fibonacci implemetation with dynamic programming.

This is taken from [a guide to implementing memoization in javascript](https://www.sitepoint.com/implementing-memoization-in-javascript/)

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
Let's say I want to see how this code works. There are several different types of breakpoints that I can use. The one I'm looking at today is the most popular: line-of-code breakpoints. I can add breakpoints by clicking on the lines of code I want to monitor:

![adding breakpoints]({{site.baseurl}}/images/adding-breakpoints.png)

Now I can click run snippet.

I have a few options at each point it stops:

1. Resume script execution
2. Step over next function call
3. Step into next function call
4. Step out of current function

There's a detailed breakdown of how these actions differ on the [Google Dev Page](https://developers.google.com/web/tools/chrome-devtools/javascript/step-code#stepping_in_action).

The most important thing to know is: 

> Use step into as your typical "one line at a time" action, as it ensures that only one statement gets executed, no matter what functions you step in and out of.

Step over executes the next line and stops at the following line.

Step out completes the rest of the function and stops at the next statement.

## Checking Out the Values

The whole point of using breakpoints is that they allow you to check all of the currently-defined properties. You can even edit properties in the script on the fly.

You can only see the call stack and the scope when the program is paused.
