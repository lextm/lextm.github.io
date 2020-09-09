---
layout: post
title: Secrets Behind JetBrains Rider and IIS Express
tags: JetBrains .NET IIS
permalink: /secrets-behind-jetbrains-rider-and-iis-express-d2ad006301e1
excerpt_separator: <!--more-->
---
{% include image.html
src="columbia-university.jpg" caption="Copyright © Lex Li. Autumn time at Columbia University." width="512px" %}

I did not realize JetBrains was working on IIS/IIS Express integration, as I uninstalled Rider from my Windows machine, and only use it on Mac. However, it was [a Stack Overflow thread](https://stackoverflow.com/questions/45560884/jetbrain-rider-access-denied-when-using-iis-express-with-windows-authenticatio) that drew my attention a few days ago.
<!--more-->

So it was surprising to see that JetBrains decided to,

* Follow Microsoft's approach to use a hidden `applicationHost.config`, but in its `.idea` folder.
* Design the relevant UI elements for configuration.

{% include image.html
src="rider-iis-express.png" caption="Figure 1: Rider dialog on IIS Express settings." %}

You can also see from the bottom left corner that IIS Express standard output is also captured and displayed in Rider so that troubleshooting issues should be easier (than VS).

> Note that the relevant UI elements might only appear in EAP/nightly builds.

I might come back to post more about the integration once it is available in a Rider stable release. Stay tuned.

Update: Jexus Manager now supports both VS and Rider's private config files,

{% include image.html
src="jexus-manager-rider.png" caption="Figure 2: Jexus Manager with Rider support." %}
