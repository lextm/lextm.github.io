---
layout: post
title: Status of IIS Out-Of-Band Modules
tags: IIS
permalink: /status-of-iis-out-of-band-modules-80a3af57c489
excerpt_separator: <!--more-->
---
When Microsoft designed and shipped IIS 7, they decided to provide a few more extensions to IIS users for free. And those were called out-of-band modules.
<!--more-->

The last important post from Microsoft on them is [this one](https://blogs.iis.net/iisoobs/updates-released-for-arr-url-rewrite-and-httpplatformhandler-including-windows-10-support), so you notice a few facts,

### OOB Modules for IIS 10 on Windows 10/Windows Server 2016 (And Above)
Only a selected set has been upgraded,

* ARR 3.0
* URL Rewrite 2.0
* HttpPlatformHandler 1.0
* IIS CORS 1.0
* ASP.NET Core (1.x/2.x, part of .NET Core) 

### Other Modules
So you might wonder what happens to other modules. Simply speaking, they are obsolete (only for IIS 10), and you cannot use them on IIS 10. (However, they remain fully supported on IIS 7.x and 8.x.)

> Forget about all other posts that tell you via registry hack how to install them.

The common issues are,

* Installer failure. (Again, don't attempt to fool the installers, as you are in fact fooling yourself.)
* Runtime crashes of IIS worker process (w3wp.exe) due to such modules.
* Other functionality related issues.

Then how to address the need of such modules if you really cannot get rid of them right now?

1. Find a Windows Server 2012 R2 machine, and use such modules on IIS 8.5. Its lifecycle would end on October 10, 2023, so you still have a few years left.
1. Find alternative modules/tools from third party vendors. As most modules are simple, alternatives are not that hard to find.
1. Develop your own if no other vendor can be found. The extensibility of IIS means you can help yourself as last resort.

### Alternatives for Some Modules
* IIS Media Services and Transform Manager users should check out Azure Media Services. The cloud services are significant an upgrade of what IIS offers, and should be considered a great opportunity to host your media files.
* FTP 7.5 has become the built-in FTP services for IIS since IIS 7.5.
* Advanced Logging has some features (custom logging) ported back as part of IIS default logging. However, its other features are no longer available. If you need those missing ones, it is very likely that you have to develop your own IIS extension. IIS 8.5 and above has [Enhanced Logging](https://docs.microsoft.com/en-us/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85) which covers most if not all features of Advanced Logging.
* Search Engine Optimization Toolkit users should fall back to manual management of related assets/files, while using other SEO services (like Google/Bing's online tools) for analysis and reports.
* Database Manager users should switch back to general tools, such as SQL Server Management Studio, or the new cross platform SQL Server Operations Studio.
* Web Farm Framework users should consider Azure Stack for private cloud usage. If you insist on running a classic web farm for IIS 10, you have to fall back to the good old days of configuring Windows load balancing service, or a hardware load balancer.

More would be added here if I find out. You can also leave a comment with your suggestion.
