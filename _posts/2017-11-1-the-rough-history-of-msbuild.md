---
layout: post
title: The Rough History of MSBuild
tags: Visual-Studio .NET
permalink: /the-rough-history-of-msbuild-cc72a217fa98
excerpt_separator: <!--more-->
---

> Again, [a Stack Overflow question](https://stackoverflow.com/questions/47061820/can-msbuild-binaries-be-used-on-their-own/47062127#47062127) triggers my ideas to write this up.

When I wrote about [C# compilers](https://blog.lextudio.com/the-rough-history-of-the-so-many-c-compilers-f3a85500707c), it is quite natural that MSBuild must be written some day. So today before my flight from YUL to JFK, let's see if I can finish the task.
<!--more-->

### Sad Story of NANT
I have been using MSBuild for a long while (ever since its introduction), and I even wrote a utility to help you run MSBuild from Windows Explroer context menu, called [MSBuild Launch Pad](https://github.com/lextm/msbuildlaunchpad).

> MSBuild Shell Extension was the first batch of utilities to enable launching MSBuild from context menu of Windows Explorer. It was recommended by Scott Hanselman in his toolbox posts. However, that project was once idle for long. I chose a different angle to implement my own tool, MSBuild Launch Pad and reused some of its code base. Letter that project was revived and ported some code from mPad.
>
> Visual Studio 2017 allows side by side installation of the whole VS. Thus, MSBuild 15 also changed its location and the way it registers into Windows, which makes it difficult to let mPad work seamlessly without an upgrade. I will update the tool once I have time or pull requests are welcome.

But what happened before MSBuild was there?

When Microsoft designed .NET Framework 1.0 around year 2000, nobody thought about a command line build system as modern as what we are familiar with today. You might use devenv.exe to build a project, but the customization can be quite difficult, and the project file format was strange either. In the meantime, ANT was quite popular in the Java world, and obviously someone cloned it as NANT for .NET (Gerry Shaw, July 5, 2001).

NANT was meant to be popular, but it also suffered issues. For example, you already had to write a separate NANT script or scripts besides your VS project files, and there would be no easy Visual Studio integration.

The design of ANT was not flawless, so later we see many other tools replacing it for Java projects (Maven, Gradle for instance). NANT inherited all the flaws, so when Microsoft started to think about its own build automation system for .NET Framework 2.0, they created MSBuild.

### MSBuild Itself
So MSBuild started to hit the market in July 3, 2004, the community welcomed it and moved away from NANT. NANT was kind of abandoned since then, but still maintained by a few guys to support newer .NET Framework releases.

> Some interesting facts can be found in [this blog post](https://notgartner.wordpress.com/2005/05/01/the-road-to-msbuild/).

It was not only .NET community that began to use MSBuild, but companies such as Borland (later CodeGear and Embarcadero) also moved their project systems (for Delphi and C++Builder) and build automation framework to MSBuild.

A few interesting projects were created to enhance MSBuild,

* MSBee, to enable .NET Framework 1.1 projects on MSBuild, https://msbee.codeplex.com
* MSBuild Community Tasks, https://github.com/loresoft/msbuildtasks/ which implements many useful tasks for your MSBuild scripts.

> I used MSBee heavily for a few years, as my very first open source project, Code Beautifier Collection for Borland Delphi was .NET Framework 1.1 only.

After MSBuild for .NET Framework 2.0, Microsoft shipped a few updates,

* MSBuild for .NET Framework 3.5
* MSBuild for .NET Framework 4.x

All of them were part of .NET Framework, so you can assume they are always there from .NET Framework installation folders.

However, MSBuild team soon realized it was necessary to lift the tooling to meet Visual Studio requirements (similar to C# compilers), so start from Visual Studio 2013, [MSBuild became a component of Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2013/07/24/msbuild-is-now-part-of-visual-studio/), and shipped separately (standalone installer) as well.

It was the development of UWP and .NET Core that raised the question of whether MSBuild should be kept or abandoned, because the introduction of project.json system seems to be cleaner and easier to use. But don't forget the fact that MSBuild has been there for more than a decade, and the whole ecosystem has been tightly connected to it. To avoid duplicate the whole system again for no good reasons, Microsoft decided to go back to MSBuild in .NET Core SDK 1.0 RTM (so as UWP in a certain release).

Of course, the experience of designing project.json was not completely wasted, and Microsoft engineers were able to heavily simplify MSBuild scripts for .NET Core based projects and introduced cool things like package references for Visual Studio 2017 (MSBuild 15).

> Note that starting from VS2017 (VS2019 included), you can install multiple VS editions side by side (Community/Professional/Enterprise/Build Tools), and each comes with its own MSBuild folder. So when you (or another tool like TeamCity) try to locate the MSBuild binaries (using tools like vswhere, or VSSetup cmdlet), it can return a list of qualified VS/MSBuild paths. This can lead to issues if you (or the tool) never expect such.

MSBuild 15 introduces the new SDK based project system, and .NET Core projects are the first to use that. There was [an open source project](https://github.com/onovotny/MSBuildSdkExtras) to add Xamarin support.

And there was also [a trick to support Mono on non Windows machines](https://github.com/dotnet/netcorecli-fsc/wiki/.NET-Core-SDK-1.0.1).

### Visual C++ on MSBuild
Visual C++ is complex, so when Borland C++Builder was using MSBuild as build engine, it is not.

Initially Microsoft created nmake as build engine, and then moved to a new generation of tool called VCBuild.

It was till Visual Studio 2010 that finally [VC++ projects were successfully migrated to MSBuild](https://blogs.msdn.microsoft.com/vcblog/2010/01/11/vcbuild-vs-c-msbuild-on-the-command-line/).

There was an open source project to support older VC++ releases.

### Mono xbuild
Mono was initially using NANT and shipping it by default. It was till December 14, 2009 that Mono 2.6 release started to ship xbuild, a clone of MSBuild for cross platform. Years later, xbuild still lacked many features of MSBuild. I suffered quite a lot personally too for my own open source projects.

> MSBuild was code named XBuild so it is coincidence that Mono later used the same name for its MSBuild clone. Similar to Mono XSP, as ASP.NET was code named XSP before its 1.0 release.

Xamarin used xbuild for their products (MonoTouch and Mono for Android initially, and later Xamarin.iOS and Xamarin.Android). Mono for Android was the first product to use MSBuild as build engine while MonoTouch used a hard code build chain. Gradually xbuild was enhanced to cover the necessary features and then Xamarin ported MonoTouch to MSBuild/xbuild.

On March 18, 2015, upon the request of Miguel de Icaza (and other Xamarin engineers), [Microsof decided to make MSBuild open source](https://github.com/Microsoft/msbuild). Soon works started to make it cross platform (like xbuild), and Mono 5.0 started to ship MSBuild as default build engine. xbuild finally phased out.

> Look for other interesting posts like this one? You can visit the [index page](https://blog.lextudio.com/all-in-one-for-the-legends-of-net-materials-43c374a01433).
