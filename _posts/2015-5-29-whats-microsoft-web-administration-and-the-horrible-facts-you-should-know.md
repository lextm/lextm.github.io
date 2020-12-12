---
layout: post
title: What's Microsoft.Web.Administration and The Horrible Facts You Should Know
tags: IIS
permalink: /whats-microsoft-web-administration-and-the-horrible-facts-you-should-know-b82f2c974da6
excerpt_separator: <!--more-->
---
I know many of you learned about IIS extensibility and came across MWA. There are not so many articles on IIS.net about it (as well as MWM), like [this](http://www.iis.net/learn/develop/extending-the-management-ui/overview-of-mwa-and-mwm-for-iis) and [this](http://www.iis.net/learn/develop/extending-iis-configuration/extending-iis-schema-and-accessing-the-custom-sections-using-mwa).

Explore them and you can see how cool it is to control IIS via a purely C# friendly API. I will let Microsoft guide you further on what is MWA, as I am just going to cover the horrible facts, which reveals to you how misunderstanding can lead to significant problems.
<!--more-->

### MWA is OS Dependent
The second link above (and many other high quality resources) shows you how to add MWA assembly to your project reference list. It must be added from `%SystemRoot%\system32\inetsrv`. And that's the ONLY way. Although IIS 7.0/7.5/8.0/8.5/10.0 look similar, they ship different versions of MWA, which are not designed to be used for each other.

The technical reasons behind that are,

* MWA is a wrapper over IIS COM interfaces. It is not something you can xcopy.
* MWA API can change slightly so as to support new features in later releases (such as adding new enumeration items to support SNI and so on).

### Stay Away From MWA NuGet Package on NuGet.org
You could hardly understand how NuGet is popular until you see the `Microsoft.Web.Administration` [package at NuGet.org](http://www.nuget.org/packages?q=microsoft.web.administration) has been downloaded more than 100,000 times. But **YOU MUST NOT USE IT IN ALL POSSIBLE WAY**.

Initially the package was published by some stranger, alexey.petriashev, who took the assembly from a Windows machine and packed it up. I tried to ask him to remove that package via NuGet.org. No reply. I contacted NuGet.org to remove that package. They replied that they could not. I even saw other Microsoft involved discussions about those problematic NuGet packages, but still nobody would like to remove them.

Then finally some day Microsoft itself needs to publish their own copy of Microsoft.Web.Administration package there, so through a process nobody knows, they reclaimed the ownership and packages 10.0.0-rc1 and the newer come from Microsoft directly. However, those new versions are specifically designed for [IIS.Administration](https://github.com/Microsoft/IIS.Administration) project, so **DON'T USE SUCH PACKAGES AS WELL**.

> It is interesting that Microsoft itself does not provide enough documentation on such.

### Other Tiny Problems You Might Notice
Comparing to the above, they seem to be so tiny, but still noticeable,

* `ServerManager.CommitChanges` returns before the changes actually take effect.
* Typically any code with MWA should be executed under a local administrator account.
* Some collections just do not support remove or clear operations. My hack is to replace the whole collection instance.
* Some APIs are schema based, while some are completely out of documentation. May the force be with you and good luck.

### Sidenote
Last but not least, I am working on a fully managed implementation of MWA as part of the Jexus Manager project. More information on that will come unexpectedly on this blog, so stay tuned.

(Updated on Feb 10, 2016: The Jexus Manager version of Microsoft.Web.Administration is now open source, https://blog.lextudio.com/2015/12/jexus-manager-the-open-source-plan/)
