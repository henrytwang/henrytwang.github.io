---
layout: post
title: Async Patterns
---

Async Patterns

Here are my notes from Kyle Simpson’s <a href="https://frontendmasters.com/courses/rethinking-async-js/" target="_blank">Learn Asynchronous Patterns course</a>. I've only included a tiny bit of the great code examples he uses, so please check out the course to see the rest.

---

**Parallel vs Async**

Parallelism — when multiple processes run exactly at the same time on multiple threads.
Asynchronicity — our program only runs on a single thread. Only one line running at any given time.

Web workers — browser objects that can run Javascript in background threads. IE10+ support. Great at handling client-side data processing, especially on mobile when it’s much less costly to use the phone CPU to render, instead of uploading data to the server. However, they are generally hard to work with, due to lack of DOM access, no shared memory with the UI, and serialization and concurrency issues. Learn more here.

Synchronous AJAX — make a POST and freeze up the page. Apparently the worst thing ever. Thankfully it’s deprecated now.

---

**Callbacks**

A callback is a continuation — we have a first half (everything outside of our callback happening now) and second half (everything in our callback happening later) of our program. There could be a gap in-between.

Callback hell — usually thought of as pyramid-of-doom-shaped nested callbacks. However, you could pull these callbacks into different functions and the pyramid shape would disappear. Did this style change solve the hellish issue? Not really. The deeper issues:

1. Inversion of control — when giving the second half of my program to an outsider, I give up control of when and in what manner I execute my code. For example, a third party tracker calls your callback 5 times, which has a function that charges your customer. So, let’s set up a boolean flag to make sure it only can run once — that’ll solve it, right? Not really. Now you also need to cover when the callback isn’t called. And what all about the other cases? You have to trust it runs on time, only once, and without error. Now, do this for the hundreds of times that you rely on callbacks.
2. Callbacks are not easily reasoned about — you will lose track of where everything is when it all runs. Our brain is single-threaded, but calls are multi-threaded. To fix this, let’s try to make things look synchronous and sequential.

Non fixes — attempts at fixing these callback issues that didn’t work.

- Separate callback — call another callback if an error occurs in the first. However, what happens if the separate callback isn’t called? What if they’re called out of order? This is not a solution and in fact, you have more trust issues now.
- Error-first style — used in Node, where an error argument can be passed to the callback, and if it exists, we can immediately run our error handling code. However, we have the same issues as we have with separate callbacks.

---

**Thunks**

A synchronous thunk is function that has everything it needs to produce a value when you call it. You do not need to pass in an argument. There is a closured state.

An asynchronous thunk is similar to a synchronous thunk in that you do not need to pass in any values when calling it, but different in that you need to pass a callback function to get the value out.

They do not solve callback hell, but by using the closure to maintain the state, we eliminate time as a complecting factor of state.

---

**Promises**

A promise is a container that eliminates time as a concern and wraps around a future value. Instead of passing a callback, we have event listeners that can watch for completion and error. The inversion of control has been uninverted — we are in complete control. For our promise, on the “then” event, we pass in a function for a resolved promise, and a function for a rejected promise.

Wait, there are still callbacks. It looks like promises didn’t solve anything. There is a difference: you can trust promises. Why? They only resolve once, it’s either a success or error, messages are passed and kept, exceptions do become errors, and they are immutable once resolved. The promise is your callback manager and you can trust it to handle all cases. However, you can still get yourself into a promise hell.

**Flow Control**

You can control the flow of multiple promises (and their errors) by chaining them. This isn’t the best pattern, but it’s better than thunks:

    doFirst()
    .then(function(){
        return doSecond(); 
    })
    .then(function(){
        return doThird();
    })
    .then(complete, error);

This is a nice little example, but what happens if you’re chaining together a much longer list of promises? And if that list’s length can vary? For example, an array of items. You could use map on this array and set a promise for each item. Then use reduce to chain together these promises.

**Abstractions**

    Promise.all

With the above, you can pass in an array of promises, fire them sequentially, and receive the results in an array ordered sequentially (and not in the order that the results arrive). It requires 100% success from all its promises. In computer science terms, this is a gate.

    Promise.race

You pass in an array of promises, and you can act on the first success/error from whichever promise finishes first.

There are many other abstractions that you should be looking to develop an instinct with, not unlike your usage of map and reduce in functional programming.

**Sequences**

Sequences are automatically chained promises

**Generators**

A syntactic form of declaring a state machine. A function you can pause and resume locally (the program isn’t paused). You use the yield keyword within the generator to pause. You call next() on the generator object to resume.

    function* generator() {
        console.log(1);
        yield();
        console.log(2);
    }

    var iterator = generator();
    iterator.next(); //1
    iterator.next(); //2

When you call next(argument), you are allowed to pass in an argument (except for the very first next call). This argument will replace the yield keyword in the generator. You can also pass an argument from within the generator to the outside by returning an object containing the argument. You do this by calling yield(argument).

Generators do not even have to finish completely. You can use it, and leave it. You could use a generator to iterate through a set of values and also to use one for synchronous-looking asynchronous code.

You should always use a generator over a promise chain. When you do, you can call promises within your generator with yield, which will move the generator forward only after the promise returns a value. The buck stops here — this pattern is what you should aim to use in your day-to-day code.

---

**Observables**

Promises can only be invoked once — they aren’t so hot by themselves in an event oriented world. We need something better. Enter observables.

An observable is like a chain of calculated fields in an Excel spreadsheet (e.g. B1 = A1 * 5). We can view a piece of data coming in and all the steps it takes as it propagates through a system.

An observable is an adaptor hooked onto an event source that produces a promise every time a new event comes through. The observable can be subscribed to in a separate way from another part of the system.

What would it look like? One of the dozen ways to set it up: we create an observable to watch for a click event on a button. Whenever that happens, we pump the event through a function that’s chained to multiple other ones, which all take some sort of data and pass it to the next. These functions can be asynchronous.

---

**CSP: Concurrency + Channels**

A channel is like a pipe that has no buffer size. The channel has back pressure — only one message can transfer through the channel at a time and both the producer and consumer must be ready with/for the message, otherwise it will not send.

CSP: communicating sequential processes. A model for managing concurrency. An example: a channel and two processes are created. One yields a put function that passes in the channel and a value. This process pauses for now. The second process is yielding with a take function that’s waiting for a value from the channel. Perfect! Both processes are now unblocked and the value from the first process is now passed to the second channel.