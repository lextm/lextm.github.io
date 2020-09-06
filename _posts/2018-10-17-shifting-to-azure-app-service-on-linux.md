---
layout: post
title: Shifting to Azure App Service on Linux
tags: Azure-App-Service Windows Linux PHP Python
permalink: /shifting-to-azure-app-service-on-linux-b216f4584b03
excerpt_separator: <!--more-->
---
{% include image.html
src="autumn-tree.jpg" caption="Copyright © Lex Li. Autumn tree near Costco, Montreal." width="512px" %}

Microsoft Azure was built upon Windows and IIS in its early days, which is not surprising. At that time, IIS 7 was shipped with PHP support, and had the capabilities to enable ASP.NET/PHP apps. Later [Microsoft added HttpPlatformHandler](https://azure.microsoft.com/en-ca/blog/announcing-the-release-of-the-httpplatformhandler-module-for-iis-8/) in 2015, which enabled other languages (Java, Node.JS, Go, Ruby, and Python) on IIS and Windows.

All look good, but we do know Windows is not equivalent to Linux, and Microsoft released [Azure App Service on Linux](https://azure.microsoft.com/en-ca/blog/general-availability-of-app-service-on-linux-and-web-app-for-containers/) last year.

So naturally, if you are hosting web apps on Azure, especially PHP/Python/Ruby users, you have both Windows and Linux backends to choose from.
<!--more-->

### Interpretation of Microsoft Documentation on Python

I just came across [an article on Azure documentation](https://docs.microsoft.com/en-us/visualstudio/python/managing-python-on-azure-app-service?view=vs-2017), which contains an important announcement,

> **Important**
>
> Microsoft is planning to deprecate the Python extensions for App Service as described in this article in favor of a direct deployment to App Service on Linux. The extensions still continue to work in the meantime. To deploy to App Service on Linux, see [Create a Python web app in Azure App Service on Linux](https://docs.microsoft.com/en-us/azure/app-service/containers/quickstart-python).

{% include image.html
src="python-announcement.png" caption="Figure 1: Microsoft announcement on Python." %}

So at least now Python users should switch to Azure App Service on Linux, and I assume users of other languages like PHP should plan the same migration. Microsoft probably should make an announcement to clear the doubt.

But if you are using just IIS and Windows, and would like to host PHP and Python applications there, does it apply to you that Linux is better?

Honestly speaking, I don't know the answer.

* PHP on IIS is still supported.
* Python on IIS via HttpPlatformHandler is still supported. Java/Go/Ruby is similar.

However, such support come without a guarantee of the surrounding ecosystem from Microsoft. So it is not astonishing if some components suddenly fail to work. Thus, I personally assume it is time to put Linux on the table.

> One benefit though is that [App Service on Linux is cheaper](https://azure.microsoft.com/en-ca/pricing/details/app-service/linux/) than App Service on Windows.

### PHP on Windows Issues

[PHP Manager for IIS](https://docs.phpmanager.xyz/getting-started/history.html) was discontinued once and I picked it up.

[PHP WinCache project](https://forums.iis.net/t/1236165.aspx?PHP+7+2+and+future+of+WinCache) is not active.

Web Platform Installer does not always ship latest PHP releases, and it no longer accepts new submissions (that's why PHP Manager 2 for IIS was not there).

### Python on Windows Issues

It is uncertain either HttpPlatformHandler or wfastcgi should be used, and both were promoted in [Microsoft documentation](https://github.com/MicrosoftDocs/azure-docs/issues/7835).

The future of HttpPlatformHandler and wfastcgi is not clear. [wfastcgi](https://pypi.org/project/wfastcgi/#history) hasn't been updated since March 2017 (or maybe it is simply solid).

### Other Noticeable News

[Go tooling on Windows was cut](https://github.com/Azure/app-service-announcements/issues/45) due to unpopularity.

Azure is a huge business to run, so it is not hard to understand that Microsoft tries to use its money on the right spots.

Update: July 10, 2020. [Microsoft officially announced the end](https://news-web.php.net/php.internals/110907) of PHP on Windows story.
