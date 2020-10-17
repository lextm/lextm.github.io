---
layout: post
title: PHP Diagnostics for IIS and IIS Express Users
tags: IIS PHP
permalink: /php-diagnostics-for-iis-and-iis-express-users-ba8beeb99f0c
excerpt_separator: <!--more-->
---
Jexus Manager already has many diagnostics wizards for different purposes,

* SSL Diagnostics, to match the original Microsoft tool developed for IIS 6.
* Visual Studio Project Diagnostics, to show the relevant settings in IIS/IIS Express configuration files and ASP.NET web project files so as to help diagnose Visual Studio debugging issues.
* Binding Diagnostics, to illustrate how site bindings work.

It is very important to add more such tools, as when I read tons of IIS questions every week, common ones become clearer and clearer. A long time challenge and the source of far too many questions is PHP on IIS, so it is time to step into that field.
<!--more-->

Microsoft guys designed this a long time ago, so as to promote the usage of IIS in PHP community. It also gave IIS a chance to support other web stacks via FastCGI protocol (such as Python).

However, configuring PHP on IIS is not an easy task because

* If you go with Web Platform Installer, you'd better on a clean server. Otherwise, the automatic configuration might conflict with other changes you made to IIS.
* If you do with the manual steps, you'd better pay much more attention to the settings, as you might easily miss an important step. The documentation is never enough on the other hand, even if Microsoft tries hard to keep it up-to-date.

There was once a great open source project called PHP Manager for IIS to ease the pains, but the original developer(s) no longer maintain it. That kind of leads to more issues, as nobody removes it from the internet and its installer does not work well with IIS 10.

I was attempting to [port PHP Manager features to Jexus Manager](https://github.com/jexuswebserver/JexusManager/issues/5), but it was never an easy task (due to the limitation of Jexus Manager obviously). But today, I decided to go the other direction, by adding a purely simple diagnostics tool for PHP on IIS, called PHP Diagnostics,

{% include image.html
src="php-diagnostics.png" caption="Figure 1: PHP Diagnostics." %}

Unlike PHP Manager, which tries to download and install PHP automatically, this tool simply analyzes IIS configuration to tell if proper PHP installation can be detected. If anything is missing or broken, it shows you where to find the proper guidance.

Current check steps implemented are,

* PHP FastCGI application registration check.
* PHP handler check.
* PHP installation version check (so you won't use an obsolete version without noticing).
* PATH environment check (which I am not sure at what time Microsoft added to the guide, but included as recommendation).

And hope it helps you out when PHP does not work well on IIS.

> Personal opinion: Try to run PHP only on Linux. PHP developers should test their apps on IIS and address compatibility issues, but they might not be able to do that due to various reasons.

Stay tuned.
