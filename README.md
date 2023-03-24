# Lexical Scope

## Learning Goals

- one
- two
- three

## Introduction

In our journey to understand scope, we've learned about the three different
levels, the scope chain, and how JavaScript traverses it. What we haven't
learned is how JavaScript decides which outer scope to place into the scope
chain for a new function.

We have an _idea_ of how, based on what we learned about the chain and nesting
scopes. Anything that a scope is nested within would be considered part of its
outer scope chain.

However, that doesn't give the whole picture. It doesn't get to the root core of
how JavaScript decides where scope is defined when a variable, function, or code
block is written. This is when the concept of lexical scope comes in.

## Lexical Scope

So, what _is_ this magical lexical scope? Before we break it down, let's look at
the following code snippet:

```js
const currentStatus = "Crocheting a sweater 🧶";

function showStatus() {
  console.log("Current Status:");
  console.log(" ", currentStatus);
}

function changeStatus() {
  const currentStatus = "Cat tangled all my yarn! 🙀";
  showStatus();
}
```

What do you think will be logged to the console when we run `changeStatus()`?

```js
changeStatus();
// => Current Status:
// =>  Crocheting a sweater 🧶
```

Did that catch you by surprise? At first glance, you might think the status
should have been `Cat tangled all my yarn! 🙀`, and you wouldn't be alone. After
all, `currentStatus` was set to that inside the `changeStatus()` function that
we called.

Then, why wasn't that the status that was logged? Lexical scope.

Every variable or function you write in JavaScript has a lexical scope. It is
**the scope in which it was originally defined**. Its home scope. It is what
decides what belongs in the scope chain of any particular variable or function.

Why does this matter? As we saw, variables and functions are meant to be used
throughout your code. They can be called upon in various different places. Or,
in terms of scope, it can be called upon in other scopes.

However, **just because a variable or function is used in the context of a
different scope from where it was defined, doesn't mean its scope chain
changes**. Its chain always remains the same, based on its lexical scope.

Lets write out a bulletpoint scope chain for our example. Here's the code again:

```js
const currentStatus = "Crocheting a sweater 🧶";

function showStatus() {
  console.log("Current Status:");
  console.log(" ", currentStatus);
}

function changeStatus() {
  const currentStatus = "Cat tangled all my yarn! 🙀";
  showStatus();
}
```

Here's the scope chain for the entire code block:

- Global Scope
  - `currentStatus`
  - `showStatus()`
  - `changeStatus()`
    - `currentStatus` (separate from the global one)

We can see that `showStatus()` and `changeStatus()` are on the same level. They
can use _eachother_, but they do not have access to anything _within
eachother's_ inner scopes. In other words, they are not part of eachother's
individual scope chain.

Meaning, when `showStatus()` is invoked in `changeStatus()`, it still only has
access to anything within its own scope and the global one. From there, what we
learned about the scope chain remains true. When JavaScript reaches the
`console.log` referring to the `currentStatus` variable, it first looks within
its own `showStatus()` scope. It doesn't find it there, so it moves up the chain
to global, where it finds the original definition.

It never looks inside `changeStatus()`'s scope because, as we learned, the scope
chain only moves _upward_, never sideways.
