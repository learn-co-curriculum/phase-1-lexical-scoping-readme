# Lexical scoping

## Overview
In this lesson, we'll learn how JavaScript decides which outer scope to place into the scope chain for a new function.

## Objectives
1. Explain the concept of lexical scoping.
2. Describe how lexical scoping informs the scope chain of a function.

## Introduction
First, a brief note. Some of the material introduced in this section might feel a bit confusing or esoteric, but, fear not, you're going to get more and more comfortable with these concepts throughout this course. If you're finding it difficult to wrap your brain around some of these more abstract concepts at this point, **don't freak out**. This is really difficult material that even seasoned programmers occasionally struggle with. After you've read the material and given it a college try, feel free to move on to the next lesson — with a mental note to return once you have more JavaScript experience under your belt. Okay, onwards and upwards!

Take a look at the following code snippet:
```js
const myVar = 'Foo';

function firstFunc () {
  console.log(myVar);
}

function secondFunc () {
  const myVar = 'Bar';

  firstFunc();
}
```

Think about what we've learned in previous lessons about how JavaScript looks up the scope chain to perform identifier resolution. Given that information, what do you think will get logged out to the console when we invoke `secondFunc()`? Let's try it out:
```js
secondFunc();
// LOG: Inside firstFunc()
// LOG: myVar is currently equal to: Foo
// => undefined
```

Did that catch you by surprise? At first glance, it might seem like `Bar` should get printed out. Inside `secondFunc()`, that string is assigned to the `myVar` variable right before `firstFunc()` is invoked:
```js
function secondFunc () {
  const myVar = 'Bar';

  firstFunc();
}
```

However, the assignment of `myVar` as `'Bar'` is **not visible to `firstFunc()`**. This is because `secondFunc()` is **not** the parent scope of `firstFunc()`.

![Lexical scoping](https://curriculum-content.s3.amazonaws.com/web-development/js/principles/lexical-scoping-readme/lexical_scoping.png)

`firstFunc()` is declared in the global scope, and, when it comes to the scope chain, JavaScript functions don't care where they are invoked. **The only thing that matters is where they are declared**. When we declare a new function, the function asks, "Where was I created?" The answer to that question is the outer environment (the outer scope) that gets stored in the new function's scope chain.

This is called _lexical scoping_, and _lexical environment_ is a synonym for _scope_ that you might encounter in advanced JavaScript materials. _Lexical_ means "having to do with words," and for _lexical scoping_ what counts is where we, the programmer, typed out the function declaration within our code. In the example above, we typed out our declaration for `firstFunc()` in the global scope, which got stored in the scope chain as the outer environment. When `firstFunc()` was invoked inside `secondFunc()`, the JavaScript engine couldn't find a local identifier matching `myVar`, so it looked up the scope chain at the outer environment. The engine finds `myVar` in the outer environment — the global scope — with a value of `'Foo'`, which is what then gets printed out to the console.

As a contrast, if we declare `firstFunc()` **inside** `secondFunc()`, then `firstFunc()`'s reference to its outer scope points at `secondFunc()` instead of at the global scope:
```js
const myVar = 'Foo';

function secondFunc () {
  function firstFunc () {
    console.log('Inside firstFunc()');

    console.log('myVar is currently equal to:', myVar);
  }

  const myVar = 'Bar';

  firstFunc();
}
```

When we invoke `secondFunc()` now, it creates a local `myVar` variable set to `'Bar'`, and then it invokes `firstFunc()`. When `firstFunc()` encounters the reference to `myVar` and realizes it doesn't have its own copy of that variable, it looks up the scope chain again. However, this time, `firstFunc()`'s outer scope isn't the global scope — it's the scope created by `secondFunc()` **because `firstFunc()` was declared in `secondFunc()`**. So `firstFunc()` uses the copy of `myVar` from the `secondFunc()` scope, which results in:
```js
secondFunc();
// LOG: Inside firstFunc()
// LOG: myVar is currently equal to: Bar
// => undefined
```

## Wrapping up
If this isn't making a ton of sense, don't sweat it too much! We're spending time on things like the _scope chain_ and the _lexical environment_ now because they're fundamental to the language, but they are not easy concepts to grasp! Investing the time and effort now will pay huge dividends throughout your JavaScript programming career. Knowing how to declare and invoke a function is great and necessary, but knowing what's actually going on under the hood during the declaration and invocation is exponentially more powerful.

When a variable contains an unexpected value, understanding the scope chain will save you countless hours of painful debugging. When you're wondering where to declare a function so that it can access the proper variables, your familiarity with JavaScript's lexical scoping will save the day. When you want to impress some new friends at a party, hit 'em with a quick lesson on how running JavaScript code consists of distinct compilation and execution phases.

<picture>
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/principles/lexical-scoping-readme/cool_party.webp" type="image/webp">
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/principles/lexical-scoping-readme/cool_party.gif" type="image/gif">
  <img src="https://curriculum-content.s3.amazonaws.com/web-development/js/principles/lexical-scoping-readme/cool_party.gif" alt="Cool party!">
</picture>

## Resources
- [JavaScript: Understanding the Weird Parts - The First 3.5 Hours](https://www.youtube.com/watch?v=Bv_5Zv5c-Ts) (Video)
