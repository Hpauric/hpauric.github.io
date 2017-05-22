---
layout: post
title: Debugging with Google Dev Tools
published: true
---

To test a new code snippet, navigate to Source

Click on `+ New Snippet`

You can add whatever code you want. In this case I'm trying out a fibonacci implemetation with dynamic programming.

This is taken from [a guide to implmenting memoization in javascript](https://www.sitepoint.com/implementing-memoization-in-javascript/)

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
Let's say I want to see how this code works.

I can add breakpoints by clicking on the lines I want to monitor:

![adding breakpoints]({{site.baseurl}}/images/adding-breakpoints.png)

Now I can click run snippet.

I have a few options at each point it stops:

1. Resume script execution
2. Step over next function call
3. Step into next function call
4. Step out of current function



