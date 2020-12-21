---
layout: post
title: Jexus Manager Is Now Open Source
tags: IIS
permalink: /jexus-manager-is-now-open-source-a48fef80a6e7
excerpt_separator: <!--more-->
---
After several months of cleaning up, yesterday I published the source code of Jexus Manager at [GitHub](https://github.com/jexuswebserver/JexusManager).

You are welcome to play with the code and report issues. Pull requests are highly appreciated if you fix any bug.

Note that there are several things you should pay attention to.
<!--more-->

### Jexus Manager's `Microsoft.Web.Administration`
This library is a clone of Microsoft's version. As previously documented, it has been open sourced a few months ago. You might stick to Microsoft's version if you like, as this clone is aiming to bring itself to Linux and OS X for Jexus web server. But it does show you how the configuration files work together, so still something useful (Microsoft's version is not open source and is mainly unmanaged).

### Jexus Manager's `Microsoft.Web.Management`
Instead of releasing it as a separate repo, this library is shipped with Jexus Manager. Compared to Microsoft's version, the clone is not mature (50% done), but does support many scenarios (wizards and so on). If someone can fully implement it, then more ugly code can be removed from Jexus Manager itself.

### Jexus Manager and Its Extensions
You can see from the code base, that this tool consists of a core shared library (`JexusManager.Shared.dll`), a core application (`JexusManager.exe`), and a few extensions (`JexusManager.Features.*.dll`). This is slightly different from Microsoft's IIS Manager, since

* IIS Manager does not have a shared library.
* IIS Manager extensions are registered in GAC and discovered via `administration.config`. Jexus Manager core application directly adds the extensions as references.
* IIS Manager extensions do not work with Jexus Manager, and vice versa. That's because Jexus Manager does not fully implement `Microsoft.Web.Management`.
* Jexus Manager uses a hack to pass configuration to extensions, via `IConfigurationService`. IIS Manager uses another service in MWM.
* IIS Manager extensions use their own names, while Jexus Manager decides to use its own names for extensions. It would be quite hard to follow IIS Manager in every way.

So now you can access the whole code base. Hope you enjoy this tool.
