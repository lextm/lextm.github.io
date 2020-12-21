---
layout: post
title: Which Class Library Project to Go in Visual Studio 2015/2017/2019
tags: Visual-Studio
permalink: /which-class-library-project-to-go-in-visual-studio-2015-2017-a48710cf3dff
excerpt_separator: <!--more-->
---
You probably often wonder why Microsoft ships so many project templates in Visual Studio 2015/2017/2019.
<!--more-->

{% include image.html
src="class-library-projects.png" caption="Figure 1: Class library project types in Visual Studio 2017." %}

Above is a screen shot of Visual Studio 2017, which quite typically shows many of the project types,

* Class Library (.NET Core)
* Class Library (.NET Standard)
* Class Library (.NET Framework)
* Class Library (Portable)
* Class Library (iOS)
* Class Library (tvOS)
* Class Library (watchOS)
* Class Library (Xamarin.Forms)
* Class Library (Android)
* Others (like UWP and so on)

Well, many people get lost as you do, as there are so many of them. So how should we understand Microsoft's logic behind the scene? I think we can divide all of them into two categories.

### Platform Specific Class Library
Class Library (.NET Framework) has been there for long since .NET Framework 1.0. We have known its rule that any such class library can only be consumed by .NET Framework apps. Such a rule also applies to other project types, targeting iOS/tvOS/watchOS/Android/UWP.

We have to rely on such class library types, as later we will see for cross platform class library types we usually cannot work against all types available on the underlying platforms.

### Cross Platform Class Library
Class Library (Portable) has been the first type of cross platform library we can use, and is also called PCL for short. It allows the code to be written against a common API set extracted from a set of platforms. Its design has many flaws, so Microsoft later introduces .NET Standard.

Class Library (.NET Standard) is the new type to replace PCL. It allows developers to write code against a much larger API surface. **Thus, if you are now developing something new and would like to support many platforms, make sure you use such a project type.**

However, there are still two special types here for .NET Core and Xamarin.Forms.

Class Library (.NET Core) is so special, that your code can actually run on many platforms (as long as .NET Core is supported there). However, unlike Class Library (.NET Standard) which can be consumed by almost all kinds of application projects, this type can only be consumed by .NET Core apps. You cannot consume such class libraries in a .NET Framework app. So if we treat .NET Core as a platform of its own, then this class type can also be analyzed as a platform specific type.

Class Library (Xamarin.Forms) is similar, as Xamarin.Forms is cross platform and supports iOS/Android/UWP (WPF and macOS support is coming). If you create a project of this type, it can only be consumed by Xamarin.Forms apps.

### How to Migrate Code Among Them
So far, my personal experience is that cut and paste is the simplest and most effective way.

> Note that in certain cases you can create a class library that targets multiple platforms (net452;netstandard2.0 for example).
