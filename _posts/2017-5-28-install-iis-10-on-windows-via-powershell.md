---
layout: post
title: Install IIS 10 on Windows via PowerShell
tags: IIS PowerShell
permalink: /install-iis-10-on-windows-via-powershell-96baf95efc2e
excerpt_separator: <!--more-->
---

Microsoft provides [a way to install IIS 8.5 via PowerShell](https://docs.microsoft.com/en-us/iis/install/installing-iis-85/installing-iis-85-on-windows-server-2012-r2#modules-in-iis-85), so can we get steps for IIS 10?

<!--more-->

### How to Learn Module Names?
You might wonder from where this IIS 8.5 table comes from, but can you try to run the following command at PowerShell and analyze its output,

``` powershell
Get-WindowsFeature
```

And you will easily see all features/sub-features (or roles/role-services) are listed (and they are just the information in the table),

{% include image.html
src="iis85-features.png" caption="Figure 1: IIS 10 Features" width="512px" %}

### OK, Why Module Names Remains The Same?

Don't be surprised that the text `45` in short module names remain while their feature names are using `4.6`.

> Should be `4.7` or `4.8` now for Windows 10 Creators Update and above.

Bad naming convention, but that's just because Windows 10/Windows Server 2016 ships with .NET Framework 4.6.x.

By keeping the same module names, Microsoft allows your previous installation scripts for IIS 8.5 to apply also to IIS 10 machines.

> That can explain why there isn't an IIS 10 specific article in Microsoft Docs.
