---
layout: post
title: Unpleasant Facts about Hangfire
tags: IIS ASP.NET
permalink: /unpleasant-facts-about-hangfire-632a3228ff8a
excerpt_separator: <!--more-->
---
{% include image.html
src="py1-pyramid.jpg" caption="Copyright © Lex Li. PY1 Pyramid show at Montreal." width="512px" %}

When people choose to use a framework, they are not only enjoying the benefits it brings, but also take ownership of the evil associated. Hangfire is one of such frameworks, so be caution.
<!--more-->

### The Thread Model: Hangfire Threads are Aliens

{% include image.html
src="threads-w3wp.png" caption="Figure 1: Threads inside w3wp.exe." %}

Microsoft designs IIS to be a multi-process system, and web applications are hosted in worker processes (w3wp.exe). So if you try to run a typical ASP.NET web application, within the worker process several threads are initialized. IIS/ASP.NET runtime are aware of such threads, so as to assert the health of the application.

This design has been used for a very long time (since 2003), so it works well with many IIS features. For example, IIS shuts down a worker process if it hasn't been processing incoming requests for a certain amount of time (idle shutdown timeout). This applies to the pink threads above in the diagram.

However, a web application with Hangfire configured usually spins off extra threads so as to schedule/execute background tasks, like the grey ones in the diagram above. Those threads are not managed by IIS/ASP.NET, and they are not processing incoming requests. Therefore, IIS idle shutdown will kill the worker process even if certain Hangfire threads are still processing scheduled tasks.

Well, [Hangfire documentation](https://docs.hangfire.io/en/latest/deployment-to-production/making-aspnet-app-always-running.html) tells that you can change IIS settings as workarounds, but does it mention that always running has its side effects? No.

### The Recommended Approaches

If you don't mind all issues brought by Hangfire, keep using it.

If you do want to go a more common way,

* On Windows Server machines, run scheduled tasks in a Windows service application.
* On a cloud platform such as AWS/Azure, run scheduled tasks in the right service (Lambda or Azure Functions).
