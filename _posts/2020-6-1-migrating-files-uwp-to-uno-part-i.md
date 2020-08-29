---
layout: post
title: Migrating Files UWP to Uno, Part I
tags: UWP UWP-Community-Toolkit Windows-10 Uno-Platform Mac
excerpt_separator: <!--more-->
---
{% include image.html
src="downtown-montreal.jpg" caption="Copyright © Lex Li. Downtown Montreal." %}

WinUI was developed as part of UWP and has been the foundation of many Windows 10 apps. [Uno Platform](https://platform.uno/docs/articles/intro.html) implements the same API surface on non-Windows platforms, so some applications can be migrated to iOS/Android/macOS (and even the Web via WebAssembly). But how difficult can such a migration be? In this series of posts, I will try to cover the story to migrate Files UWP, a medium size open source file explorer, to macOS.
<!--more-->
### What is Files UWP?

It is interesting that Microsoft keeps the classic Windows Explorer on Windows 10, instead of rewriting it in UWP. But a group of developers do make a UWP version file explorer, as below, and publish it as an open source project on GitHub.

![](https://raw.githubusercontent.com/files-community/files-uwp/master/Files/Assets/FilesHome.png) {:width="512px"}

By checking its code base, we can see typical usage of XAML/MVVM, as well as file system related API usage. So nothing is terribly complex.

We will follow [the Uno migration guide](https://platform.uno/docs/articles/migrating-apps.html) to see what to do next.

### Organizing the Code

The guide is pretty short and not in the step by step fashion. So I have to guess sometimes what exactly I am supposed to do.

Theses are the steps I did to get the initial working directory on my machine,

1. Clone Files UWP repo from GitHub.
1. Install Uno project templates, https://platform.uno/docs/articles/get-started.html
1. Open Files UWP solution in Visual Studio 2019.
1. Add a "Cross-Platform App (Uno Platform)" project to that solution. In fact, VS created a bunch of projects.

So finally a new folder called Files_Uno is added to [the repo](https://github.com/lextm/files-uwp/tree/uno), and it contains many projects for different platforms.

### Migrating the Code

I copied the files from original Files folder to Files_Uno | Files_Uno.Shared and Files_Uno | Files_Uno.UWP. Then compile and run the UWP project to confirm that everything continues to work.

> Note that a lot of details are not listed here, as they are not quite important for macOS.

Then it is time to switch to the macOS project and try to compile it, and there are hundreds of failures as you can imagine.

Don't worry. We can conquer them one by one and learn from that.

### Typical Errors

Namespace related errors are quite common.

1. `Window.Current` must be rewritten as `Windows.UI.Xaml.Window.Current`, because now `Page` class has its own `Window` property (which maps to a Cocoa object).
1. Uno does not yet have 1Microsoft.UI.Xaml.Controls1, so have to use equivalent controls from 1Windows.UI.Xaml.Controls1.
1. `TabView` from `Microsoft.UI.Xaml.Controls` is not implemented, so have to switch to `TabView` from `Microsoft.Toolkit.Uwp.UI.Controls`. Note that the two controls have significant differences, so switching to another requires both changes to XAML and code behind.
1. `FileIO.*` are not implemented yet, so have to rewrite them as `System.IO` based calls.
1. Bindings to static items are not yet supported by Uno, so have to wrap static items with instance items and then bind to the wrappers.

So after [this commit](https://github.com/lextm/files-uwp/commit/0375745c65184eec16184e0c1859f5a74e047499) the code base finally compiles and runs on macOS. But the result is in fact disappointing, as a blank window is displayed.

What happens and how to move on? We will visit that in Part II.
