---
layout: post
title: How to Replace BackgroundWorker with Async/Await and Tasks
tags: .NET C# Visual-Studio Microsoft
permalink: /how-to-replace-backgroundworker-with-async-await-and-tasks-80d7c8ed89dc
excerpt_separator: <!--more-->
---
{% include image.html
src="clouds-over-woods.jpg" caption="Copyright © Lex Li. Clouds over the woods in Montreal." width="512px" %}

`BackgroundWorker` was introduced in .NET Framework as an easy way to do asynchronous tasks in Windows Forms applications. Its beauty lies in the simplicity and just enough encapsulation. You don't need to know much about threading (such as `Thread` and `ThreadPool` classes) to use this tiny little class in your code base.

However, when .NET Framework 4.x came with `Task` based classes (Task Parallel Library), and especially 4.5 where async/await was introduced, you really need to know that the pattern of `BackgroundWorker` should come to an end.

Well, the only challenge is, how we can convert the code base from `BackgroundWorker` to async/await, which I will show you in this post.
<!--more-->

### The Sample Project in Initial State

[The project](https://github.com/lextm/backgroundworker-sample/commit/d2ca2509e06cc7bdbe9492cb54c181cdd704e22e) is hosted on GitHub, and is Windows Forms based.

To make full use of `BackgroundWorker`'s capability, both progress reporting and cancellation are utilized. As you can see it is rather simple, and easy to understand.

This pattern does have its problems, like

* You need to keep in mind that `DoWork` event handler runs in a separate thread, not the UI thread (aka main thread), though that's not quite obvious from the code.

> `ProgressChanged` and `RunWorkerCompleted` event handlers, however, are executed in the UI thread. That's why there the text of label can be updated directly.

* Cancellation required several pieces to be set, and if you miss one piece the result won't meet your expectation.
* The heavy task should be a sync function call (like `Thread.Sleep`), so if you need to call an async function you won't know how to in a glance.

### Removing BackgroundWorker
Well, now it's time to kill the bird. And you can easily see the changes here in [the commit](https://github.com/lextm/backgroundworker-sample/commit/2e4cdf37c14b4e049407ea91db82dbefb125cc64).

Obvious parts are,

* `CancellationTokenSource` and `CancellationToken` are needed now to implement cancellation.
* There is no need to have separate methods and everything can come together in a single method.
* Mostly importantly the heavy task must be in a `Task` object now, and here I use `Task.Delay` as an example.

But still you must keep in mind the basic tips on async/await,

* async void is only used for the button click event handler as Microsoft documented.
* While the heavy task is executed in the background (and awaited), changing the text of label is in UI thread, as after the await line the execution context is back.

### Conclusion

Task and async/await have been very important addition to .NET ecosystem, so use them whenever possible and that's going to make your life a lot easier.
