---
layout: post
title: How to Understand .NET API Browser (Dec 2017)
tags: Visual-Studio .NET
permalink: /how-to-understand-net-api-browser-dec-2017-16fe068ab072
excerpt_separator: <!--more-->
---
Microsoft tries to use new tools to build .NET documentation, which is good. The benefits for the community are,

* The documentation tool is open source, aka DocFX (https://github.com/dotnet/docfx).
* Everyone can now submit their ideas and comments via GitHub pull requests (https://github.com/dotnet/docs).

However, the tooling and its results right now (Dec 2017) are far from perfect, and can be even misleading in certain cases. While we can accept the current state and look for the future, I am sharing below how to interpret certain things in the right way.
<!--more-->

### "Applied to" Section — Which Platforms Can I Use The Class?

Let's use [the page](https://docs.microsoft.com/en-us/dotnet/api/system.security.principal.windowsidentity?view=netcore-2.0#Applies_to) for `WindowsIdentity` here as an example, where we easily see from "Applied to" section this class can be used on multiple platforms.

Don't be surprised if you only see 4.x for .NET Framework. I guess Microsoft guys are not yet ready to add .NET Framework 3.5.1 (that platform should be still supported), so I opened [a GitHub issue](https://github.com/dotnet/docs/issues/3931) for them to review.

### "Assemblies" Section — Which NuGet Package Should I Use?

Well, if you do know what is .NET Core and its tooling, then reading the new API documentation becomes more challenging, as now we cannot assume a type is simply there in an assembly (.NET Framework convention), but need to know which NuGet package I should add as reference (MSBuild 15 PackageReference).

So if you intend to use `WindowsIdentity`, which NuGet package should you use? There seems to be no hint at all. Really?

In fact, you can scroll to the top of the page, and check the following,

> Namespace: System.Security.Principal
> Assemblies:System.Security.Principal.Windows.dll, mscorlib.dll

Well, you have to really know .NET Framework enough so as to know by instinct the meaning behind "Assemblies" section.

Here `mscorlib.dll` means that if you are working on a .NET Framework project, this type comes directly from the core library assembly (which in most cases you don't even need to explicitly add as a reference, by some MSBuild magic).

The important part that most people would neglect is `System.Security.Principal.Windows.dll`, but for me it is the only thing that indicates the NuGet package you need to add, aka `System.Security.Principal.Windows`. Cannot believe it? But it is true.

Even this kind of discovery requires you have quite good experience with .NET Core, so you know the convention there is that the NuGet package name matches the assembly name (any exception in the future?).

Thus, I opened [another GitHub issue](https://github.com/dotnet/docs/issues/3932) to see if Microsoft would improve in this area.

### Others

I am trying to use the same tooling (DocFX) to build [a new documentation site](https://help.sharpsnmp.com/) for my #SNMP products, and it shows a good start.

But I could not yet get my own "Applied to" section due to [the broken merge command](https://github.com/dotnet/docfx/issues/2289) in DocFX.

So let's see what would happen in the coming weeks, and I will update this post or write a new one.
