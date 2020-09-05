---
layout: post
title: Brief History of MonoDevelop
tags: Linux Mono MonoDevelop
permalink: /brief-history-of-monodevelop-93b1d4011978
excerpt_separator: <!--more-->
---
{% include image.html
src="construction-site.jpg" caption="Copyright © Lex Li. Construction site, Montreal." width="512px" %}

There is [a Wikipedia article about MonoDevelop](https://en.wikipedia.org/wiki/MonoDevelop). However, I don't think it contains the necessary history events on this product, so here I provide an alternative version.
<!--more-->

> Note that I also [blogged about MonoDevelop status](https://blog.lextudio.com/status-of-monodevelop-xamarin-studio-253da80d022c) in the past.

When Miguel de Icaza announced the Mono project at OSCON on June 30, 2001, clearly a free and open source IDE was part of the plan (to replace Visual Studio, as on Linux there was no other option). The early work on Mono CLR, C# compiler, and GTK# binding made it possible to write such an IDE based on GTK#.

### Forking SharpDevelop

Before they got started, the SharpDevelop project started by Mike Kruger in 2000 was already a mature IDE to enable C# development on Windows. So instead of starting from scratch, it was a natural decision to fork SharpDevelop, and build a GTK# based user interface to replace the original Windows Forms based one. MonoDevelop 0.1 was released on February 4, 2004.

SharpDevelop and MonoDevelop shared common project system and many other components. But due to the significant changes of .NET Framework 2.0 and above, SharpDevelop was upgraded with important changes like,

* Switching to MSBuild based project system, and
* Switching to WPF.

MonoDevelop didn't catch up with such, due to the slow progress on Mono. The MSBuild clone, aka xbuild, was not released (as part of Mono 2.6) until December 14, 2009.

### Assisting Unity and MonoTouch

Though falling far behind SharpDevelop, MonoDevelop was given a rare chance to grow quickly due to the collaboration between Mono and Unity3D announced on February 26, 2008. Unity3D uses MonoDevelop as the basis of its own gaming IDE, which not only increases the user base, but also moves the project forward.

In 2008 and 2009, the Mono team at Novell started to develop MonoTouch, which enables C# based iOS application development. This ambitious plan gave MonoDevelop an important role to play as code editor. After integration with Xcode and Interface Builder was done, the final result was a commercial extension with iOS development support, released as part of MonoTouch 1.0 on September 14, 2009.

Right after that, Mono for Android was developed, and existence of xbuild made it possible to move Mono for Android build system to the MSBuild compliant engine. Mono for Android later could support both MonoDevelop and Visual Studio as IDE. MonoTouch build system also switched to xbuild in a later release.

On May 25, 2011, Xamarin was launched by Miguel de Icaza and Nat Friedman after the Novell/Attachmate layoff. All rights of MonoTouch and Mono for Android was handed over from SUSE to Xamarin on July 18.

### Commercializing

On February 20, 2013, Xamarin announced its Xamarin 2.0 platform, where its own commercial IDE called Xamarin Studio was shipped for Windows and Mac. It shares many components with MonoDevelop 4.x-6.x, but features commercial extensions to enable iOS/Android/Mac development. Though Xamarin Studio is commercial product with Xamarin brand, it remained free for most users on Windows/Mac if they didn't use the commercial extensions.

> Due to upgrade roadblocks, Unity IDE remained on MonoDevelop 5.x for a long while, till on January 5, 2018 an announcement was made to replace it with third party IDEs such as Visual Studio and Rider.

Microsoft acquired Xamarin in February 24, 2016. Visual Studio for Mac was announced on November 16, as the successor of Xamarin Studio. Many Visual Studio for Windows features were copied to the Mac counterpart. It shares components with MonoDevelop 7.x-8.x. However, Xamarin Studio for Windows was discontinued. Visual Studio for Mac Community edition is free for most users.

MonoDevelop 8.x is still in active development. Linux packages for its 7.x releases are well maintained, while Mac and Windows installers haven't been updated for a long while (initially due to the existence of Xamarin Studio, and later due to Visual Studio for Windows and Mac).

Clearly there has been complaints on Microsoft for the issues in MonoDevelop for Windows and Linux. But for a company like Microsoft the situation is that several similar products are in the same space (Visual Studio for Windows/Visual Studio for Mac/Visual Studio Code), so its strategies turn to treat its own user base with highest priorities. MonoDevelop, an open source project, is moving far away from the spotlight.

Meanwhile, the creation of Visual Studio for Mac and its aggressive plan to adopt Visual Studio for Windows core assets lead to a tough situation. On one hand, VS for Mac/MonoDevelop receives a great opportunity to copy directly the necessary code from its Windows counterpart. More importantly that code becomes open source for everyone's sake (after a lengthy Microsoft internal process for legal review). On the other hand, the more code this process brings in, the more breaking changes we see between MonoDevelop 7.x and 8.x.

Many people only see one side of the coin, and complain that they have no Windows/Linux builds of MonoDevelop 8.x to use. That can be short sighted, as MonoDevelop 8.x do receive quite a lot of useful upgrades. But Microsoft does mess things up by focusing too much on the Mac story.

It would be rather difficult to predict when we shall be able to compile MonoDevelop 8.x flawlessly on Windows, or Linux, as the experts in this field are now busy bringing more new things into the code base for Mac.

Should you really care? Visual Studio Community edition (Windows) and VSCode (Linux) can cover many scenarios already.

### Sidenotes

You can compile MonoDevelop on Mac and Windows [from source code](https://www.monodevelop.com/download/). I recently started to ship MonoDevelop for Windows MSI installer via [my personal fork](https://github.com/lextm/monodevelop-windows).

In the future, MonoDevelop will be able to run on CoreCLR, as revealed in the [.NET 5 announcement](https://devblogs.microsoft.com/dotnet/introducing-net-5/) on May 6, 2019,

> "Run MonoDevelop and then Visual Studio for Mac on CoreCLR."

> Update: 2020-8-19 [MonoDevelop ends]({% post_url 2020-8-19-the-end-of-monodevelop %}).
