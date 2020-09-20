---
layout: post
title: Migrating Jexus Manager to .NET Core 3.0
tags: Visual-Studio .NET IIS
permalink: /migrating-jexus-manager-to-net-core-3-0-f1c41ae50572
excerpt_separator: <!--more-->
---
{% include image.html
src="mont-royal.jpg" caption="Copyright © Lex Li. Mont Royal, Montreal." width="512px" %}

Microsoft shipped .NET Core 3.0 Preview 1 with Windows Forms and WPF support. So immediately I started to migrate Jexus Manager to this new platform. A few hours ago [Jexus Manager 13.0.0 Preview 1](https://github.com/jexuswebserver/JexusManager/issues/104) was released, which runs fine on .NET Core 3.0 Preview 1.
<!--more-->

The version number change is obvious, because from this version on, Jexus Manager no longer depends on .NET Framework, though its functionality depends on .NET Framework still so as to manage ASP.NET configuration.

The migration was much smoother than I thought, so here comes a blog post that covers the details.

### Preparation

Telerik published [a great article](https://www.telerik.com/blogs/how-to-update-wpf-winforms-app-to-dotnet-core-3) showing what you might do.

In my case, I ran dotnet new winforms and played with the simplest sample project first.

It is very interesting to analyze the new project format,

``` xml
<Project Sdk="Microsoft.NET.Sdk.WindowsDesktop">
  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>netcoreapp3.0</TargetFramework>
    <UseWindowsForms>true</UseWindowsForms>
  </PropertyGroup>
</Project>
```

So there is no hard coded assembly name or default namespace, and the contents can easily be copied over to other project files.

Noticeably it uses a special SDK for Windows desktop, and uses `WinExe` as output type. Such all changes how the final executable behaves.

### Project Files

To upgrade Jexus Manager, the first task is to determine which project files need to be changed to the new format. To be safe, I modified [the core projects](https://github.com/jexuswebserver/JexusManager/commit/d883643902c9155c2c886da3f78c9e1cdb4fd9ab).

> There is not yet official documentation on how class libraries with WinForms bits should look, so I used the .NET Standard class library template and added the necessary reference,
> ``` xml
>    <PackageReference Include=”Microsoft.WindowsDesktop.App” Version=”3.0.0-alpha-27128–4" />
> ```
>
> This brings in all the Windows Forms assemblies.

Note that Jexus Manager depends on many NuGet packages, and also has project references (custom WinForms controls). Even though such dependencies are for .NET Framework, I can add references to them directly in the new project. By allowing `netcoreapp3.0` projects to consume `net4xx` assemblies, Microsoft indeed saves me tons of hours.

### Compilation Errors

You can still hit tons of errors when compiling the source code with new project files. Luckily most of them should be simple to solve, by revising references, adding other packages, such as

* Windows Compatibility Pack for .NET Core
* System.Windows.Extensions

You might have to get rid of certain legacy code,

* System.Web
* Microsoft.VisualBasic

`System.Web` is not all about ASP.NET WebForms, because there are still a few utility classes widely used. It is not clear whether Microsoft would port them over, but you should report issues to CoreFX if you like.

Microsoft already decided to port most of `Microsoft.VisualBasic` but it also takes time. In my case, I could not find `LikeOperator` so I used a similar code snippet instead.

Some of the dependencies are also troublesome. For example, `Mono.Security` works on .NET Framework, but one method simply throws PlatformNotSupportedException on .NET Core, because .NET Core changed one of the methods it depends. It would be boring to patch `Mono.Security` so I fully migrated to Bouncy Castle.

You might also pay attention to things like `Resources.resx`, as you might need to manually specify its generator (`ResXFileCodeGenerator`).

### Compilation Result

.NET Core SDK generates `.exe` files this time, you can launch your application in a more convenient way, but right now it is not clear to me what's the exact runtime dependencies of such `.exe`. At least they rely on .NET Core 3.0 SDK or runtime, and I didn't find a way to generate self-contained deployment.

### Known Issues

You should keep an eye on [the issues list](https://github.com/dotnet/winforms/issues), as the more people migrate their apps, the more new issues appear. The ones that bother Jexus Manager a lot are,

* [UAC shield icon does not appear](https://github.com/dotnet/winforms/issues/192).
* [System.ComponentModel.Design.CollectionEditor is missing](https://github.com/dotnet/winforms/issues/177).
* [Resx support is broken](https://github.com/dotnet/winforms/issues/251).

### Overall Experience

So it was a lot faster to finish the migration than my original estimate. The output at this stage also looks pretty good. The known issues are not quite blocking either, and I hope in .NET Core 3.0 Preview 2 they will all be resolved.

If you are working on WinForms/WPF apps, I believe you should start your migration right now.
