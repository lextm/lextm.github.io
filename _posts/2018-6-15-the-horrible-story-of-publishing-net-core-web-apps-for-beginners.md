---
layout: post
title: The Horrible Story of Publishing .NET Core Web Apps For Beginners
tags: Visual-Studio .NET IIS
permalink: /the-horrible-story-of-publishing-net-core-web-apps-for-beginners-6121662dd8c4
excerpt_separator: <!--more-->
---
{% include image.html
src="snow-ladder.jpg" caption="Copyright © Lex Li. Snow covered ladder in Quebec City." width="512px" %}

Generally speaking, ASP.NET Core is cool, and the recent 2.1 release is even cooler with all kinds of new goodies. However, this post is not aiming to praise Microsoft on what they do the best, but the worst currently for ASP.NET Core/IIS beginners.
<!--more-->

> To assist you identifying the typical issues, now Jexus Manager has [ASP.NET Core Diagnostics](https://docs.jexusmanager.com/tutorials/ancm-diagnostics.html),

{% include image.html
src="ancm_diag.png" caption="Figure 1: ASP.NET Core Diagnostics in Jexus Manager" %}

### Famous Issue 1: ASP.NET Core Module on IIS

I cannot recall when Microsoft decided to make ASP.NET Core module for IIS a second installer (out of .NET Core SDK for Windows). So whenever a developer gets familiar with .NET Core with merely the SDK installed on Windows, and attempts to publish the binaries for IIS to host, BOMB, a famous 500.19 error page happens.

{% include image.html
src="oob_500.png" caption="Figure 2: IIS 500.19 error." %}

> The Error Code field must be 0x8007000d.
>
> If the Error Code field was 0x80070005, the application pool identity has no access to the web content folder, which you should fix by granting the permissions.

It has been discussed so many times over the internet, and you can always find beginners on ASP.NET Core puzzled that in fact they need to install something extra for IIS. Of course, Microsoft makes it very clear by stating it [in the official documentation](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/?view=aspnetcore-2.1&tabs=aspnetcore2x#install-the-net-core-hosting-bundle).

But,

* ASP.NET Core module is part of IIS Express, which makes it super natural that people expect it to be part of IIS.
* ASP.NET Core module is not part of the SDK for Windows, and not part of Visual Studio.

Therefore, if a new developer comes and tries, he/she won't be able to know what to acquire and install easily, unless the error page shows at the last minute.

> Note that you also need to update ASP.NET Core module to match the actual runtime, as ASP.NET Core 2.2 introduced ASP.NET Core module v2, which is different from v1.

### Famous Issue 2: Framework Dependent Deployment vs. Self Contained Deployment

Again, Microsoft makes [very clear documentation](https://docs.microsoft.com/en-us/dotnet/core/deploying/).

But it is still not easy for beginners to hit this page and fully understand the concepts. Thus, when they ship binaries from one machine to another, there is still possibility that the apps can break, if

 * FDD is used.
 * The runtime is not installed on the target machine.

Clearly, the developer must know that the runtime ought to be installed separately for FDD.

### Famous Issue 3: Where To Configure HTTPS

Ideally you should install the certificate on the reverse proxy server (IIS/Apache/nginx). Then the browser/reverse proxy connection is HTTPS enabled, while the reverse proxy/Kestrel connection can be HTTP.

I think of course the latter can be configured as HTTPS enabled as well, but it would take your some time to figure out how to.

### Possible Solution

I don't know if there can be a complete solution to address both issues, but personally I believe the following might help a little bit,

* Let dotnet publish generates a warning message after execution.
* Let Visual Studio publishing wizard displays a similar warning message after execution.
* If FDD is used, warn the developers that the .NET Core runtime is a must on the target machine as a separate installation.
* If the target platform is Windows, warn the developers that ASP.NET Core module for IIS can be mandate (if they do host on IIS).

What would you suggest then?
