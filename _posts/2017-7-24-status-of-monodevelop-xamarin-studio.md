---
layout: post
title: Status of MonoDevelop/Xamarin Studio
tags: Mono
permalink: /status-of-monodevelop-xamarin-studio-253da80d022c
excerpt_separator: <!--more-->
---

> Disclaimer: I don't work for Microsoft/Xamarin, so this post is just based on my personal observation.

It is rather interesting that even after the acquisition of Xamarin by Microsoft, there is no official statement on Xamarin product lifecycle. This is not something convenient, if you are used to the facts that most [Microsoft products' lifecycle](https://support.microsoft.com/en-ca/lifecycle/search) is well governed.

So, what should we use today to develop with Xamarin products? Below is a summary from me, unofficial but should help you understand the status.
<!--more-->

### Visual Studio (Recommended)

You should use Visual Studio on Windows (Community, Professional, Enterprise, and Ultimate) and then install Xamarin for Visual Studio. [Visual Studio 2015/2017](https://www.visualstudio.com/vs/whatsnew/) obviously provides the streamlined installation experience, but if you use an older VS release, it would be pretty tough.

You should use [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) on macOS.

Currently there is no Visual Studio for Linux, so there would be no way to develop Xamarin apps (iOS/Android and so on) on Linux. If you like, go and vote for [this feature request](https://visualstudio.uservoice.com/forums/121579-visual-studio-ide/suggestions/18433768-visual-studio-for-linux-os).

### Xamarin Studio (Obsolete)

You should now say goodbye to Xamarin Studio, no matter on Windows and macOS.

### MonoDevelop

The status of MonoDevelop is always a misery. However, it was the origin of everything we have today.

According to [the timeline I maintained](http://corefx.strikingly.com/), MonoDevelop 0.1 was released on Feb 4, 2004, as a port of SharpDevelop to Linux and Mono. It has evolved for years to be a full featured IDE, and the most recent releases are,

* MonoDevelop 5.x, the last release included in most Linux official distributions if you don't use Xamarin latest bits. It is quite old, and you should avoid it whenever possible.
* MonoDevelop 6.x, a relatively new release which empowers the commercial product, Xamarin Studio (on Windows and macOS).
* MonoDevelop 7.x, a stable release, which enables commercial product, Visual Studio for Mac initial release.
* MonoDevelop 8.x, the latest release, which enables latest Visual Studio for Mac.

> Note that Visual Studio for Mac can be seen as MonoDevelop plus commercial extensions (Xamarin Studio as well). The commercial extensions are not open sourced. Some examples are the .NET Core debugger extension, and the new code editor (ported from Visual Studio 2017).

Well, usually only maintainers of MonoDevelop/Xamarin Studio/Visual Studio for Mac extensions should be interesting in such facts.

You should only use MonoDevelop if you are on Linux.

In all cases, you should use the latest tools,

* Windows, Visual Studio + Xamarin for Visual Studio
* macOS, Visual Studio for Mac
* Linux, MonoDevelop 7.x (though it cannot develop any Xamarin apps)

> If you do need MonoDevelop for Windows to finish something Visual Studio does not support, I created [a CI job on AppVeyor](https://github.com/lextm/monodevelop-windows). You can download the artifacts (like an MSI installer) from there (currently matching MonoDevelop 7.8).

> MonoDevelop 8.x is a tough topic, so I gave [a new post](https://blog.lextudio.com/brief-history-of-monodevelop-93b1d4011978) with more information.
