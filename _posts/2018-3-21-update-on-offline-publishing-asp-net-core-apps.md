---
layout: post
title: Update on Offline Publishing ASP.NET Core Apps
tags: .NET
permalink: /update-on-offline-publishing-asp-net-core-apps-e7894a84bd46
excerpt_separator: <!--more-->
---
{% include image.html
src="linus-bikes.jpg" caption="Copyright © Lex Li. Linus bikes in Boston." width="512px" %}

I [blogged about how to publish an ASP.NET Core app against an internal NuGet server](https://blog.lextudio.com/offline-publishing-asp-net-core-apps-cf22f45158af).

A few days ago, Microsoft happened to ship a new release of 2.0.6. Then what kind of things are needed to upgrade your internal infrastructure to support this new release?
<!--more-->

First, you need to download and install the latest .NET Core SDK (2.1.101 today), so that all necessary NuGet packages are added to the local fallback folder. This saves you all the efforts to download them one by one from NuGet.org.

Second, modify the package reference in your project. It should point to `Microsoft.AspNetCore.All 2.0.5` before, and now to `Microsoft.AspNetCore.All 2.0.6`. Without this, packages restored in `obj\project.assets.json` would only contain the old versions.

Third, change the publishing command line to something like `dotnet publish /p:RuntimeFrameworkVersion=2.0.6 -r linux-x64 --self-contained`. The `/p` part forces .NET Core CLI to target the new runtime.

The command might report missing packages, as of course you don't have them yet on the internal NuGet server,

> error NU1102: Unable to find package runtime.linux-x64.Microsoft.NETCore.App with version (>= 2.0.6)

Simply follow the error messages to locate all missing packages and then put them to your NuGet server. You should notice that the missing ones are `runtime.linux-x64.Microsoft.NETCore.*` packages of 2.0.6 version, which are simple upgrade of the previous packages of 2.0.5 version.

Stay tuned.
