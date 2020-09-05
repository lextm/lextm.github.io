---
layout: post
title: The Rough History of .NET Core Debuggers
tags: Microsoft Visual-Studio .NET
permalink: /the-rough-history-of-net-core-debuggers-b9fb206dc4aa
excerpt_separator: <!--more-->
---
{% include image.html
src="pavillion-e-ets.jpg" caption="Copyright © Lex Li. Maison des étudiants - Pavillion E , ETS." width="512px" %}

There are several .NET Core debuggers out there from different vendors, and this post covers the major ones and their stories.
<!--more-->

### .NET Core Debugger from Microsoft, aka vsdbg

.NET Core was born with a debugger, of course from Microsoft, called vsdbg. With a long history and reputation in Windows based debuggers, it is not difficult for Microsoft to create such a tool on Windows, and also make it cross platform for macOS and Linux. Naturally, this debugger is a core asset for .NET Core development and also part of Visual Studio (Windows/Mac) and Visual Studio Code.

> vsdbg is not open sourced and not everyone paid enough attention to the licensing terms of vsdbg (and its various components), until one day JetBrains entered the arena and shipped its .NET IDE called Rider.

### .NET Core Debugger from JetBrains

It was in February 2017 that [JetBrains realized](https://blog.jetbrains.com/dotnet/2017/02/15/rider-eap-17-nuget-unit-testing-build-debugging/) the the debugging functionality of Rider was built upon a Microsoft NuGet package `Microsoft.VisualStudio.clrdbg` which in fact was not licensed to any third party IDEs like Rider.

Microsoft soon pulled that package out of NuGet.org to avoid further confusion, and JetBrains disabled .NET Core debugging in Rider EAP 17.

Luckily JetBrains was also famous for its technical excellence, so the engineers were able to write its own Windows side .NET Core debugger [in just a week in EAP 18](https://blog.jetbrains.com/dotnet/2017/02/23/rider-eap-18-coreclr-debugging-back-windows/). Mac and Linux support came after about 4 months, [in EAP 23](https://blog.jetbrains.com/dotnet/2017/06/16/rider-eap-23-net-core-debugger-back-code-cleanup/).

Since then, JetBrains has been able to continuously refine its own .NET Core debugger to implement more features. Recent achievements include but are not limited to,

* Remote debugging was supported [since 2018.3](https://blog.jetbrains.com/dotnet/2018/11/29/remote-debugging-comes-rider-2018-3/).
* Edit & Continue was supported [since 2019.1](https://blog.jetbrains.com/dotnet/2019/04/16/edit-continue-just-time-debugging-debugger-improvements-rider-2019-1/).

However, JetBrains .NET Core debugger is also proprietary, so only ships as part of the commercial IDE, Rider.

### Samsung .NET Core Debugger, aka netcoredbg

[Samsung joined .NET Foundation Technical Steering Group](https://dotnetfoundation.org/blog/2016/06/27/samsung-join-tsg) back in June 27, 2016.

That's an important move for this hardware centric company where it embraces Microsoft .NET Core technologies to help boost its Tizen operating system.

On November 16, 2016, [Visual Studio Tools for Tizen](https://dotnetfoundation.org/blog/2016/11/16/google-join-tsg) was released, and Samsung open sourced its [].NET Core debugger](https://github.com/Samsung/netcoredbg) (not only for Tizen, but also for other operating systems like Windows/Mac/Linux) on GitHub.

Compared to the debuggers from Microsoft/JetBrains, this open source debugger lacks of many important features, but as the solely free tool without licensing problems it enables all kind of possibilities.

### Sidenote on MonoDevelop

Visual Studio for Mac is built upon MonoDevelop. However, Microsoft does not license vsdbg to be used in MonoDevelop. That's why earlier this year I finished [the extension to enable .NET Core debugging in MonoDevelop with the Samsung debugger](https://blog.lextudio.com/samsung-net-core-debugger-and-monodevelop-80a6ea4bcab3).

You should be aware that Samsung's effort on its .NET Core debugger seems to end (no active development since April 2019), and netcoredbg does not work quite well with .NET Core 3 and above.

There should be a free and open source debugger for .NET Core 3 and .NET 5, so hope someone step in and take over the responsibilities.
