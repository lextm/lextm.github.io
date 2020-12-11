---
layout: post
title: How to Resolve mscorlib Related Issues in Visual Studio/MSBuild
tags: Visual-Studio .NET
permalink: /how-to-resolve-mscorlib-related-issues-in-visual-studio-msbuild-84965aaf6f47
excerpt_separator: <!--more-->
---
For many .NET developers, mscorlib is something miserable as they never pay much attention to it. In fact, they should learn about it, especially when Visual Studio and MSBuild report an error about it.
<!--more-->

### What is mscorlib?
We might go to [Mono's source code](https://github.com/mono/mono/tree/master/mcs/class/corlib) to see what is mscorlib, as that's better than using a decompiler.

OK. A glance can show you how many namespaces are included (please ignore `Mono.*` which are Mono specific). So when we write in C# `using System;` or `using System.IO;`, usually the consumed classes come from `mscorlib`, not `System.dll`. All .NET assemblies have `mscorlib.dll` as a reference, so it is part of the foundation of Base Class Library.

### How It Is Referenced By Others Normally?
If you create a new C# project in Visual Studio (such as Windows Console applications), you perhaps don't see a reference to mscorlib.dll. Why? Well, that's because of an MSBuild/CSC trick Microsoft implements.

First, most csproj files do not have `<NoStdLib></NoStdLib>` defined under `<PropertyGroup>`. In this way MSBuild uses the default value of `false` in its execution. This finally translates to [a switch](http://msdn.microsoft.com/en-us/library/fa13yay7.aspx) of `csc.exe` called `/nostdlib-`.

Second, `csc.exe` automatically picks up the proper `mscorlib.dll` (the details on that resolution itself are worth another post).

Finally, `csc.exe` generates the assembly for your project, and the reference to `mscorlib.dll` is written into that file.

The above knowledge can be easily gained by turning on [MSBuild logging](http://msdn.microsoft.com/en-us/library/vstudio/ms164311.aspx) via `/verbose` switch.

### Specially Case
Normally you never need to worry about the reference to `mscorlib.dll` if you read above. Now let's visit the most common exceptional cases where you should care about `mscorlib.dll`.

Because of the fact that .NET targets multiple platforms, some project types requires a special `mscorlib.dll` to be added as reference. The very first example was .NET Compact Framework, where its `mscorlib.dll` contains a reduced set of classes. Then more platforms follow, such as Xamarin.iOS, Xamarin.Android, Windows RT, and Windows Phone.

So if you happen to have a CF project file open at hand (like [this one](https://github.com/lextm/sharpsnmplib/blob/master/SharpSnmpLib/sharpsnmplib.cf35.csproj)), you can see how VS is smart enough to add `<NoStdLib>true</NoStdLib>` for us, and also insert a reference to `mscorlib.dll` to CE version, under `C:\Program Files\Microsoft.NET\SDK\CompactFramework\v3.5\WindowsCE\mscorlib.dll`.

> Note that in this case when MSBuild translates the `csc.exe` command line, a `/nostdlib+` is added.

OK, so if you compile any project and meet the following errors, make sure review what I typed above and soon locate the problem in your project file,

* "Predefined type 'System.Object' is not defined or imported"
* "A reference to 'mscorlib' could not be added. This component is already automatically referenced by the build system."

Happy hacking.
