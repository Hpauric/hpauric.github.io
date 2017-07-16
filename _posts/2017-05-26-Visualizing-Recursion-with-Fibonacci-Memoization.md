---
layout: post
title: Visualizing Recursion with Fibonacci Memoization
published: true
---
## Visualizing Recursion with Fibonacci Memoization

How exactly does this code work?

It is implementing recursion and memoization.

```javascript
var fibonacci = (function() {
  var memo = {};

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
```

This code declares a self-invoking function that returns a function

```javascript
function f(n) {...}

  return f;
  ```
  
  While Google Dev Tools are very powerful, they can't show you where in a recursion loop you are. You can click on functions in the call stack, and it will show you the variable and property values of that function, and it's find the function called it (hint: it's the previous function in the call stack).
  However it's difficult to visualize the position in the recursion tree.
  
  ![recursion-tree.jpg]({{site.baseurl}}/images/recursion-tree.jpg)
  *image_caption*
  
  We can visualize recursion with this amazing tool
  
  [Python Tutor](http://www.pythontutor.com/visualize.html#mode=display)
  
  Another visualizer
  
  https://visualgo.net/en/recursion
  
  ## Passing Functions
  
  We can see how functions are passed with this code:
  
  ```javascript
  function myFunction(n){
  if(n === 2)
  console.log("you sent 2");
  else
  console.log("not two");
}

var testIffy = (function() {
  console.log("Hello");
  return myFunction;
})(); 
testIffy(2);
```

testIffy is immediately invoked and returns the function `myFunction`. This function is passed testIffy's argument.
```javascript


function memoize(func) {
  var memo = {};
  var slice = Array.prototype.slice;

  return function() {
    var args = slice.call(arguments);

    if (args in memo)
      return memo[args];
    else
      return (memo[args] = func.apply(this, args));

  }
}


var fibonacci = (function() {

  function f(n) {
    var value;
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(n - 1) + f(n - 2);
    

    return value;
    }

  return memoize(f);
})();
fibonacci(10);

var fibonacciModified = (function() {

  function t(n, t1, t2) {
    var value;
      if (n === 0)
        value = t1;
      else if(n === 1)
        value = t2;
      else   
        value = t(n - 2,t1,t2) + Math.pow(t(n-1,t1,t2),2);
    

    return value;
    }

  return memoize(t);
})();

fibonacciModified(4,0,1);
//F(n) = F(n-1) + F(n-2), n>1

//t(i) = t(n-2) + Math.pow(t(n+1),2);
```






