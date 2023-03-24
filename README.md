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
learned about the scope chain remains true.

When JavaScript reaches the `console.log` referring to the `currentStatus`
variable, it first looks within its own `showStatus()` scope. It doesn't find it
there, so it moves up the chain to global, where it finds the original
definition.

It never looks inside `changeStatus()`'s scope because, as we learned, the scope
chain only moves _upward_, never sideways.

In sum, when it comes to the scope chain, **JavaScript variables and functions
don't care where they are referred to or invoked. The only thing that matters is
where they are declared**.

## Keeping Lexical Scope in Mind

Now that we know what lexical scope is and why our code didn't work as expected,
we can make informed changes to it. There are two different ways we can go about
it that keep lexical scope in mind:

1. Make `changeStatus()` part of `showStatus()`'s individual scope chain.
1. Make `changeStatus()` update the globally scoped `currentStatus` variable.

Let's try both. We can decide at the end which one we think is better.

First, let's **make `changeStatus()` part of `showStatus()`'s individual scope
chain**.

To make something part of another scope chain, we have to nest them together.
Which way do you think should we nest these functions? Should `changeStatus()`
be nested inside `showStatus()`, or should it be the other way around?

Think about what we know of the scope chain. The chain is only traversable
one-way, from inside out. If we were to make `changeStatus()` the innermost
function, it _still_ would not be part of `showStatus()` individual scope chain.

```js
const currentStatus = "Crocheting a sweater 🧶";

// Incorrect nesting!
function showStatus() {
  console.log("Current Status:");
  console.log(" ", currentStatus); // =>  Crocheting a sweater 🧶

  function changeStatus() {
    const currentStatus = "Cat tangled all my yarn! 🙀";
    showStatus();
  }
}

changeStatus(); // => ReferenceError: changeStatus is not defined
```

Additionally, `changeStatus()` would now belong inside the `showStatus()`
function scope. It would no longer be accessible in the global scope, as it only
exists within `showStatus()`.

Instead, we have to nest `showStatus()` inside `changeStatus()`. This keeps
`changeStatus()` as part of the global scope, _and_ makes it part of
`showStatus()`'s individual scope chain.

```js
const currentStatus = "Crocheting a sweater 🧶";

// Correct nesting!
function changeStatus() {
  const currentStatus = "Cat tangled all my yarn! 🙀";

  function showStatus() {
    console.log("Current Status:");
    console.log(" ", currentStatus);
  }

  showStatus();
}

changeStatus();
// => Current Status:
// =>  Cat tangled all my yarn! 🙀
```

The updated bulletpoint scope chain now looks like:

- Global Scope
  - `currentStatus`
  - `changeStatus()`
    - `currentStatus` (separate from the global one)
    - `showStatus()`

Now, when JavaScript performs variable lookup for `currentStatus` as referenced
in `showStatus()`, it will first look inside its own scope. When it doesn't find
it, it will go up one level on the chain to `changeStatus()`. It will find it,
resolve the variable lookup, and use the value as defined in `changeStatus()`.
It will never reach the global scope version as it doesn't need to go up any
further along the chain.

That's the first way to fix the issue.

The second fix is to **make `changeStatus()` update the globally scoped
`currentStatus` variable** instead of creating a new one in its own scope.

This avoids nesting all together. In fact, this fix only requires two little
changes. See if you can spot them:

```js
let currentStatus = "Crocheting a sweater 🧶";

function showStatus() {
  console.log("Current Status:");
  console.log(" ", currentStatus);
}

function changeStatus() {
  currentStatus = "Cat tangled all my yarn! 🙀";
  showStatus();
}
```

Did you see the changes? The first one was changing the global variable from
being defined with `const` to `let`. This allows us to reassign a new value to
it later on, which leads to the second change. Instead of declaring a new
version of `currentStatus` with `const` inside `changeStatus()`, we _reassign_ a
new value to the global variable.

When the variable lookup in `showStatus()` occurs, it will act as it originally
did. It will look in its own scope, then to the global scope. However, because
the _global_ `currentStatus` gets reassigned a new value before that lookup even
occurs, it now outputs the status we expect: `Cat tangled all my yarn! 🙀`

> <details><summary>Seeing these two changes, which fix do you personally think is best?</summary>
> <p>As previously cautioned, it's best practice to avoid nesting too many things when you can. Always opt for the simplest solution, when it makes sense. </p>
>
> <p>For our particular example, what if we just wanted to show the status without changing it first? With the nested fix, that wouldn't be possible at all, as the showStatus() function only exists within changeStatus(). We are forced to change the status before we can show it.</p>
>
> <p> Of course, you could nest <i>another</i> function inside changeStatus() that handles updating the status separately. Except now you've created three functions, two nested in one, when you could have just stuck with two separate ones.</p>
>
> <p>Nesting gets messy exponentially quickly. When you don't need to, don't!</p>
> </p></details>

## Conclusion

Lexical scope refers to something's home scope. Much like ourselves, a function
or variable's lexical scope lives in just one place. We can visit friends,
cafés, work, school, wherever - but our address will always remain at home.
Lexical scope is the same. Functions can be invoked wherever possible, but its
home scope, and therefore its accessible scope chain, will always remain the
same.

As with all things scope, this too may feel like some nebulous concept. Scope is
not something you consciously do or write. In a way, it just happens. However,
where you write things determines how they're scoped and what other parts of
code they have access to. It is an ever-present concept, always there even when
you're not thinking about it.

As you move through the rest of this course and further on your programming
journey, just knowing what all this scope magic is will help immensely.

When a variable contains an unexpected value, understanding the scope chain will
save you countless hours of painful debugging. When you're wondering where to
declare a function so that it can access the proper variables, your familiarity
with JavaScript's lexical scoping will save the day. When you want to impress
some new friends at a party, hit 'em with a quick lesson on how everything
defined in JavaScript has a home (well, a lexical scope), and isn't that just
beautiful?
