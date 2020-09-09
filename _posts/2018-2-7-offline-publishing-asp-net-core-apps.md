---
layout: post
title: Offline Publishing ASP.NET Core Apps
tags: Visual-Studio .NET IIS
permalink: /offline-publishing-asp-net-core-apps-cf22f45158af
excerpt_separator: <!--more-->
---
{% include image.html
src="biosphere-montreal.jpg" caption="Copyright © Lex Li. Biosphere in Montreal." width="512px" %}

It is my personal view that Microsoft designed .NET Core/ASP.NET Core with little offline experience in consideration, which makes it quite difficult for an enterprise with firewalls and proxies to fully embrace the technology. Thus, a post like this would discuss the investigation I just finished and hope it can help companies with external network access restrictions to set up their internal environment properly.
<!--more-->

### A Quick Review

So the following test case would be used,

* Make a test web app, by running `dotnet new mvc`. Of course, this generates an ASP.NET Core MVC sample project.
* On a machine with internet access, `dotnet publish -r win-x64` will generate the x64 deployment binaries for Windows. You can use `dotnet publish -r win-x64 --self-contained` if you need self contained deployment.

### Offline/Internal Network

To simulate the offline/internal network situation, I created an Azure virtual machine and enabled Hyper-V on it. Then I created a Hyper-V guest of Windows Server, where I can switch its network adapter to either external/internal easily.

I enabled external access, so that I can install .NET Core SDK (2.1.4 right now) and Visual Studio Code (with C# extension). Then of course, I have to disable external access to see what would break.

`dotnet new mvc` still works in this case, and give me the same test project. It is a little bit surprising, as I assume here it needs internet access to download the project template. So somehow Microsoft ships the default templates in the SDK.

`dotnet restore` or `dotnet build` does work, as .NET Core SDK has a fallback folder (will discuss later) with all needed packages cached.

However, `dotnet publish -r win-x64` failed,

``` text
error: Unable to load the service index for source https://api.nuget.org/v3/index.json
```

as the default NuGet source setting contains nuget.org which is not accessible at this moment.

Here I use `dotnet new nuget` to create a `nuget.config` file here to override the default sources,

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <packageSources>
        <clear />
        <add key="test" value=".\local" />
        <add key="fallback" value="C:\Program Files\dotnet\sdk\NuGetFallbackFolder" />
    </packageSources>
</configuration>
```

The `local` folder must be manually created, and we can leave it empty right now. You should now understand where the fallback folder is, right?

However, `dotnet publish -r win-x64` would not work again for other errors, like

``` text
error NU1101: Unable to find package runtime.win-x64.Microsoft.NETCore.App. No packages exist with this id in source(s): fallback, test
```

Well, there are multiple packages missing as the errors indicate. So they must be manually downloaded from nuget.org and put into the `local` folder.

> Note that the package version number should be the latest, such as 2.0.5.

> Note that there should be an easy way to locate the packages, which would be added here if I find out.

Well, another run of dotnet publish -r win-x64 won't succeed yet,

``` text
error NU1605: Detected package downgrade: Microsoft.NETCore.DotNetHostResolver from 2.0.5 to 2.0.0. Reference the package directly from the project to select a different version.
```

The error message is misleading, as to fix it, you should change the command to `dotnet publish /p:RuntimeFrameworkVersion=2.0.5 -r win-x64`. This would force all packages to be matching .NET Core 2.0.5 release.

OK. You should now see no error and the deployment binaries are published.

To publish `linux-x64` you can run `dotnet publish /p:RuntimeFrameworkVersion=2.0.5 -r linux-x64` which should generate another list of missing packages, which you can later deploy to the `local` folder.

> Note that `local` folder here can be replaced by the internal NuGet server your company uses. This internal server must support the necessary NuGet protocols, ideally matching NuGet 3.4 and above. You need to verify on your own, as there are so many NuGet server products out there.

### Sidenotes

A real world web app can be much more complicated, which might need more NuGet packages to be added to `local` folder. Right now I see no easy tooling to scan dependencies, except letting `dotnet restore` and `dotnet publish` show the errors. Anyway, the experience is not very pleasant, and hope future tooling can improve in this area.
