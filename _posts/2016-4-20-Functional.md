---
layout: post
title: Functional-Lite JS
---

Notes from Kyle Simpson's <a href="https://frontendmasters.com/courses/functional-js-lite/">course</a>.

Side effects- running a function modifies something in the “outside world”, or outside the scope of the function. Every time you run the function, you’re going to get a different result. This is as opposed to “pure” functions that end in the same result after the 1,000,000th call.

Composition- taking the output of Function A and making that part of the input for Function B.

```javascript
function mult(x,y) {
  return x*y;
}

function add(x,y) {
  return x+y;
}

mult(add(3,4), 5);
```

Immutability — item cannot be mutated. vars can be mutated, while consts cannot… unless we’re talking about an array. This const will never point to another object, but you can edit the values of the array.

If you signal something is immutable, don’t attempt changes to it. If the object is frozen, you’ll get an error. If the object is not frozen and you change it, you’re making a side effect. Javascript technically lets you make these changes, but as a functional programmer, you consciously choose not to do it. So, make a copy, or produce your own set of values.

Passing an argument is the same thing as assigning it to a parameter. Huh? For simple primitives (null, undefined, boolean, number, string), when assigned to a variable, and a second variable points at the first variable, the second variable gets a copy. Non-simple primitives (object, array) are held by reference, so new variable assignments always point to the same primitive.

---

Closure- a function that remembers the variables around it, even when the function is executed elsewhere.

A closure could be modifying one of the variables “around it” (e.g. ). This variable would be considered in an internal state, since it’s wrapped in the closure, but changing it will still be considered a side effect. Not quite as functional or pure.

So, how to use a closure more functionally?

```javascript
function sumX(x) {
  return function(y){
    return x + y;
  }
}
var add10 = sumX(10);
add10(3) = 13;
add10(11) = 21;
```

There are no side effects when you call sumX.

Recursion- recursive functions can be broken down into two parts: the base case and the recursive call (the function calls itself from within). Mostly great in theory, as we’re limited in CPU and memory. Multiple stack frames are created when recursive functions are called. You’re averaging around 10–15 deep in your day to day code. Modern browsers have limits in the tens of thousands. Typically powerful not in practicality, but more in graceful code. However, if you use proper tail call recursion, you can get O(1) usage of memory, as you use the same stack over and over.

List transformation- you can use a map function on a massive list and have the transformations happen on different threads. Map is functional in that it returns a new list based on transformed values of the old list. However, some do not like how we have to use map:

```javascript
[1,2,3].map(function(x){
  return x + 2 
});
```

We are chaining off an object, instead of using only functions. It is easy to use though.

Note: if you see this:

```javascript
[1,2,3]
  .map(doubleIt)
  .map(tripleIt);
```

You should look into composing the two functions in order to run them at the same time on the value.

List exclusion — filter out values from array if it does not return true in the function passed in.

List reduction — compose values together from within a list. Not concat, but reduce. There’s an initial value that you have to think through beforehand depending on what you’re doing to it. Numbers, promises, functions.

List iteration- don’t do this one? The only time this format would be useful is if the predicate had a side effect. Not functional. Don’t use forEach. Except when you want to use it. Just be aware of it.