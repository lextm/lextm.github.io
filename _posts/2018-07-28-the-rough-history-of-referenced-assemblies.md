---
layout: post
title: The Rough History of Referenced Assemblies
tags: Visual-Studio MSBuild .NET Mono
permalink: /the-rough-history-of-referenced-assemblies-7d752d92c18c
excerpt_separator: <!--more-->
---
{% include image.html
src="clouds-canal.jpg" caption="Copyright © Lex Li. Clouds reflected in the canal, Montreal." width="512px" %}

This post tries to fill the gaps left in the compiler history post. You can find all other posts I wrote in [the index page](https://blog.lextudio.com/all-in-one-for-the-legends-of-net-materials-43c374a01433).
<!--more-->

### .NET Framework 1.x/2.x/Compact Framework

When Microsoft released such old releases, the compilers (C#/VB) always compile your program against the core assemblies (like mscorlib ) in GAC or .NET Framework installation folder.

### .NET Framework 3.x/4.x/Silverlight/UWP

Microsoft started to ship referenced assemblies when they released .NET Framework 3.0, according to [an old blog post](https://blogs.msdn.microsoft.com/msbuild/2007/04/12/new-reference-assemblies-location/) from MSBuild team.

They followed that in the following releases, like 3.5 and 4.x.

Referenced assemblies are used by the compilers at compile time, to verify metadata, and are very important due to the in-place upgrade feature of .NET Framework 4.x.

For example, if you already upgrade the system to .NET Framework 4.5, but want to compile a program that can run on raw .NET Framework 4.0, obviously you cannot let the C# compiler run against core assemblies in GAC (v4.5) as they already contain 4.5 specific API and metadata. Instead, you point the C# compiler to `%ProgramFiles%\Reference Assemblies\Microsoft\Framework\v4.0` so the proper metadata can be located.

As a neat feature, Microsoft made full use of it, when later releasing other platforms, such as Silverlight and UWP. Portable Class Library also depends on their referenced assemblies to guide the compilers.

> You should also notice that the referenced assemblies of PCL use version number of "2.0.5.0". None of the assemblies contain anything other than metadata, so the version number does not matter at all. At runtime, your program uses the actual underlying platform version of the assemblies (4.0.0.0 is many cases).

### Target Frameworks and Developer Packs

So referenced assemblies are grouped into different folders, and each groups map to a specific API surface,

* .NET Framework 4.0
* .NET Framework 4.0.1
* .NET Framework 4.0.2
* .NET Framework 4.5
* many more.

When you work on a class library project in Visual Studio, the first thing you should be aware of is the target framework option of the project, because it controls how the compiler locates the referenced assemblies on your machine, and what API surface your code is compiled against.

Microsoft creates installers for most of the new target platforms, and they are called [Developer Packs](https://www.microsoft.com/net/download/visual-studio-sdks).

> Note that only .NET Framework 4.5.2 and above have Developer Packs. For legacy versions (4.0.x and 3.5.x) you need to install via .NET Framework SDK.
>
> Visual Studio relies on the presence of such Developer Packs to show a certain target framework in the project option, so make sure you install such before working on a project.

### SKUs
Because target frameworks are in fact API surface (classes and methods), a certain .NET Framework (like 4.7.2) can support code compiled against many target frameworks,

* .NET Framework 4.0
* .NET Framework 4.0 Client Profile
* .NET Framework 4.0.1
* .NET Framework 4.0.1 Client Profile
* …
* .NET Framework 4.7
* .NET Framework 4.7.1
* .NET Framework 4.7.2

Such are called SKUs, and can be found in registry under `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319\SKUs`.

OK, now you know why in `app.config` you might see XML elements like,

``` xml
<configuration>
  <startup> 
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
  </startup>
</configuration>
```

The `sku` attribute is used by .NET CLR to validate against the installed SKUs. So if you run a program relying on .NET Framework 4.7.2 SKU on .NET Framework 4.6.2 machine, an error is expected.

> Note that [there is another registry key](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) to determine installed .NET Framework version, `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP`, which is irrelevant to SKUs or target frameworks or Developer Packs.

### Mono and Xamarin

The extensibility of referenced assemblies and MSBuild makes it possible to let programs compile against Mono assemblies metadata. But more importantly, Xamarin platforms can later hook to the same system, to enable iOS and Android development.

> However, Microsoft never released its own tooling to create referenced assemblies. So people found their own ways. For instance, I still remember one of the PCL extension project (PCLCrypto maybe) writes its own empty classes and compile them to a "referenced assembly".
>
> Also notice that initially Mono/Xamarin guys cannot easily implement PCL support because the materials only shipped with Visual Studio, and were not released under a suitable license. Later [Microsoft decided](https://blogs.msdn.microsoft.com/dotnet/2013/10/14/portable-class-library-pcl-now-available-on-all-platforms/) to release the referenced assemblies separately and allowed Mono to pack it. And [Mono/Xamarin acted](https://blog.xamarin.com/microsofts-pcl-reference-assemblies/) swiftly.

### .NET Core/.NET Standard

Microsoft finally made [an official feature](https://github.com/dotnet/roslyn/issues/2184) in Roslyn to output referenced assemblies.

But .NET Core compilation no longer uses the old referenced assemblies folder.

Now it is time to ship referenced assemblies in your NuGet package, and Oren Novotny wrote [a great blog post](https://oren.codes/2018/07/09/create-and-pack-reference-assemblies-made-easy/) to guide you.

Enjoy it :)
