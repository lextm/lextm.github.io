---
layout: post
title: The Rough History of The So Many C# Compilers
tags: Visual-Studio .NET
permalink: /the-rough-history-of-the-so-many-c-compilers-f3a85500707c
excerpt_separator: <!--more-->
---

> Again, this post came from [a Stack Overflow answer](https://stackoverflow.com/questions/22814922/difference-between-c-sharp-compiler-version-and-language-version/46995130#46995130) I gave.

Microsoft made C# open standard in early 21 century, so everyone can implement their own C# compilers. But most people use Microsoft's, from a team led by Anders Hejlsberg. Thus, at the very beginning let's see how Microsoft versioned its own compiler.
<!--more-->

### Microsoft Compilers as Part of The Framework
It was very important that C# compiler shipped with .NET Framework initially, because one of the legacy project types, aka ASP.NET Web Site, requires the compiler to always be there on a system. (The C# compiler, as well as VB.NET compiler, would be called at runtime.)

So naturally, the C# compiler was conventionally versioned after the .NET Framework version shipping it, such as

* csc.exe 1.0 (?) for .NET Framework 1.0 (implements C# 1.0 specification, aka ISO-1).
* csc.exe 2.0 (like Microsoft (R) Visual C# 2005 Compiler version 8.00.50727.8745) for .NET Framework 2.0 (implements C# 2.0 specification, aka ISO-2, as well as 1.0 for compatibility).
* csc.exe 3.5 (like Microsoft (R) Visual C# 2008 Compiler version 3.5.30729.8763) for .NET Framework 3.5 (implements C# 3.0, and older versions).
* csc.exe 4.0 (?) for .NET Framework 4.0 (implements C# 4.0, and older).
* csc.exe 4.x (like Microsoft (R) Visual C# Compiler version 4.7.2053.0) for .NET Framework 4.5 and above (implements C# 5.0, and older). Note that the version numbers vary a lot (from 4.x to 12.x) based on the .NET Framework on your machine (4.5.0 to 4.7.1).

> Note that the actual version strings printed by csc.exe can vary, so the numbers shown above are for demonstration purposes only.

You should also notice that all above compilers were written in C/C++, which means C# was not compiled by C#, different from languages such as TypeScript. To make use of the compiler infrastructure for more scenarios (such as IntelliSense in Visual Studio), Microsoft kicked off an internal project to rewrite the C# compiler (and VB.NET compiler) in C#, and announced this project in 2008 on PDC as the Roslyn project.

These compilers can exist side by side. For example, on Windows Server 2016, you might have the following under .NET Framework installation folder,

* C# compiler 2.0
* C# compiler 3.5
* C# compiler 4.x

### Microsoft Compilers (Roslyn) as Part of Visual Studio
Starting from Visual Studio 2015, the 1.0 release of Roslyn based C# compiler became the default compiler, but Microsoft decided to make it part of Visual Studio (also as NuGet package), instead of being part of .NET Framework.

Visual Studio 2017 ships with 2.0 version of Roslyn based C# compiler.

* Roslyn csc.exe 1.x (?) implements C# 6.0 and older. Shipped with VS2015.
* Roslyn csc.exe 2.x (like Microsoft (R) Visual C# Compiler version 2.4.0.62122 (ab56a4a6)) implements C# 7.x and older. Shipped with VS2017.

### Compiler Feature: Cross C# Language Versions
As shown above, if you use C# compiler 2.0 to compile a C# 1.0 project, everything should simply work, as a new C# compiler implements the latest C# specification, as well as all previous ones. Recent C# compilers support the `langversion` switch for this.

But you should notice that certain language features, such as async-await, have different implementation approaches. So if you C# compiler 4.x to compile a project, the compilation result would be possibly different from the result from Roslyn C# compiler 1.x or 2.x. (Can be proved if you disassemble the compiled assemblies.)

### Compiler Feature: Multi-Targeting
The best benefit of the recent C# compilers (starting from 4.x) is that you can use a compiler of 4.x to compile project against older .NET Framework releases, such as 3.5.

This proves that many language features, such as null propagation, as indeed just compiler tricks, and do not rely on the underlying .NET Framework bits.

> Note that this is different from `langversion` switch.

### The Mono C# Compilers
Mono project used to have its own C# compilers (the first prototype was done by Miguel de Icaza), such as

* mcs.exe (Mono C# compiler 1.x) to match Microsoft C# compiler 1.x.
* gmcs.exe (Mono C# compiler 2.x, with generics support) to match Microsoft C# compiler 2.0 and 3.5.
* dmcs.exe (Mono C# compiler 4.x, with dynamic support) to match Microsoft C# compiler 4.x

The compilers were separate projects, and later merged when IKVM.Reflection was created to address the challenges.

> As Mono C# compilers were written in C# on day one, it actually implemented many cool features of the Roslyn compiler long before Microsoft was able to ship Roslyn.

Once Microsoft shipped Roslyn and made it open source and cross platform, Mono started a long term plan to adapt it. And starting from Mono 5.0, Mono started to ship the Roslyn C# compiler. Mono's own C# compilers are no longer (actively) maintained.

> Note that Mono compilers were also used in Xamarin products.

The Mono project has [a dedicated page](https://www.mono-project.com/docs/about-mono/languages/csharp/) for its C# compilers.

### The dot42 C# Compiler
[The dot42 product](https://github.com/dot42/dot42) was once a solution to use C# to develop Android applications. It has its own C# compiler to emit Android instructions.

The dot42 product is no longer maintained.

### The RemObjects C# Compiler
RemObjects initially sells their Pascal compiler, called Chrome, and later renamed to Oxygene. Such a compiler is being updated frequently and is recently able to compile code for .NET/Mono/JVM/ObjC runtimes.

Then RemObjects brought other languages to the same compiler infrastructure, such as C# and Swift (called Silver). Thus, they in fact create a "RemObjects flavor" of C# with some platform extensions to cover ObjC runtime and JVM (beyond Microsoft specifications).

### The GNU C# Compiler and Others
Except Mono there were other projects implementing C# and .NET, such as [DotGNU](https://www.gnu.org/software/dotgnu/).

They also have their own compilers, but with almost no influence in the market.

### The C# Parser in ReSharper and Rider
JetBrains Rider and ReSharper share a C# language parser. Though not a full compiler, it supports multiple C# language specifications.
This parser has a few unique features, and [JetBrains decided to keep using it](https://blog.jetbrains.com/dotnet/2014/04/10/resharper-and-roslyn-qa/), instead of switching to Roslyn (DevExpress CodeRush moved to Roslyn).

> Look for other interesting posts like this one? You can visit [the index page](https://blog.lextudio.com/all-in-one-for-the-legends-of-net-materials-43c374a01433).