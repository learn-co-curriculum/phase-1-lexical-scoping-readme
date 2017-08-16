# Lexical scoping

## Overview
In this lesson, we'll learn how JavaScript decides which outer environment to place into the scope chain for a new function.

## Objectives
1. Explain the concept of lexical scoping.
2. Describe how lexical scoping informs the scope chain of a function.

## Introduction
Take a look at the following code snippet:
```js
const myVar = 'In the global scope.';

function firstFunc () {
  console.log(myVar);
}

function secondFunc () {
  const myVar = 'In the secondFunc() scope.';

  firstFunc();
}
// => undefined
```

Think about what we've learned in previous lessons about how JavaScript looks up the scope chain to perform identifier resolution. Given that information, what do you think will get logged out to the console when we invoke `secondFunc()`?

```js
secondFunc();
// LOG: "In the global scope."
// => undefined
```

At first glance, it might seem like `"In the secondFunc() scope."` should get printed out. Inside `secondFunc()`, that string is assigned to the `myVar` variable right before `firstFunc()` is invoked:
```js
function secondFunc () {
  const myVar = 'In the secondFunc() scope.';

  firstFunc();
}
```

However, the assignment of `myVar` as `"In the secondFunc() scope."` is **not visible to `firstFunc()`**. This is because `secondFunc()` is **not** the parent scope of `firstFunc()`.

![Lexical scoping](https://user-images.githubusercontent.com/17556281/29369661-408f9342-8271-11e7-91ec-236c30f7a716.png)

`firstFunc()` is declared in the global scope, and, when it comes to the scope chain, JavaScript functions don't care where they are invoked. **The only thing that matters is where they are declared**. When we declare a new function, the function asks, "Where was I created?" The answer to that question is the outer environment (the outer scope) that gets stored in the new function's scope chain.

This is called _lexical scoping_, and _lexical environment_ is a synonym for _scope_ that you might encounter in advanced JavaScript materials. _Lexical_ means "having to do with words," and for _lexical scoping_ what counts is where we, the programmer, typed out the function declaration within our code. In the example above, we typed out our declaration for `firstFunc()` in the global scope, which got stored in the scope chain as the outer environment. When `firstFunc()` was invoked inside `secondFunc()`, the JavaScript engine couldn't find a local identifier matching `myVar`, so it looked up the scope chain at the outer environment. The engine finds `myVar` in the outer environment — the global scope — with a value of `'In the global scope.'`, which is what then gets printed out to the console.

## Wrapping up
Some of the material introduced in this section might feel a bit confusing or esoteric, but, fear not, you're going to get more and more comfortable with these concepts throughout this course. Things like the _scope chain_ and the _lexical environment_ are fundamental concepts within the language, and investing the time and effort now will pay huge dividends throughout your JavaScript programming career. Knowing how to declare and invoke a function is great and necessary, but knowing what's actually going on under the hood during the declaration and invocation is exponentially more powerful.

When a variable contains an unexpected value, understanding the scope chain will save you countless hours of painful debugging. When you're wondering where to declare a function so that it can access the proper variables, your familiarity with JavaScript's lexical scoping will save the day. When you want to impress some new friends at a party, hit 'em with a quick lesson on how running JavaScript code consists of distinct compilation and execution phases.

![Cool party!](https://user-images.githubusercontent.com/17556281/29349418-d385f406-8227-11e7-9994-a49681e5dea4.gif)

## Resources
- [JavaScript: Understanding the Weird Parts - The First 3.5 Hours](https://www.youtube.com/watch?v=Bv_5Zv5c-Ts) (Video)
