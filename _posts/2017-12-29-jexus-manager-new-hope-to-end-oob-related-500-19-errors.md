---
layout: post
title: Jexus Manager, New Hope to End OOB Related 500.19 Errors
tags: Visual-Studio .NET IIS
permalink: /jexus-manager-new-hope-to-end-oob-related-500-19-errors-193854de0d4d
excerpt_separator: <!--more-->
---

When Microsoft designed IIS 7.0, those guys must be quite proud of their achievement. That release is so significant that the later releases 7.5/8.0/8.5/10.0 almost require no big changes. The architecture is quite flexible, and makes extending IIS a fun.
<!--more-->
Microsoft itself later released several interesting Out-Of-Band (OOB) modules to extend IIS functionality. I blogged about the current status of them [here](https://blog.lextudio.com/status-of-iis-out-of-band-modules-80a3af57c489).

Of course, that's fantastic for server administrators, as new features can come for free, and no IIS upgrade is required.
However, I personally think Microsoft left a bug there in IIS, which makes the experience horrible for new comers. What is it? Let's get started from the error page,

{% include image.html
src="500-19.png" caption="Figure 1: OOB 500.19 error page." %}

I [blogged](https://blog.lextudio.com/jexus-manager-troubleshooting-iis-configuration-with-jexus-manager-for-iis-express-read-only-mode-3aa89c332f79) about how Jexus Manager might give better exception information.

But is that intuitive enough and eases all the pains? I suddenly realize the answer is still No. Sad.

What can I do now to help? Well, Jexus Manager 2.1.0.56 and above shows this new dialog,

{% include image.html
src="oob-dialog.png" caption="Figure 2: OOB in details." %}

Do you like it? Finally Jexus Manager can analyze the unrecognized element, and decide which OOB module you might forget to install, and point out from where you can acquire the bits.

Enjoy it :)
