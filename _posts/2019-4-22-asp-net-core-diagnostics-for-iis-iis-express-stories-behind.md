---
layout: post
title: ASP.NET Core Diagnostics for IIS/IIS Express, Stories Behind
tags: .NET IIS ASP.NET Visual-Studio
permalink: /asp-net-core-diagnostics-for-iis-iis-express-stories-behind-90b4e8229aad
excerpt_separator: <!--more-->
---
{% include image.html
src="flight-canada.jpg" caption="Copyright © Lex Li. A flight to New York City." width="512px" %}

ASP.NET Core on IIS/IIS Express shouldn't be hard, as they all come from Microsoft. However, we all know it has been a mess since the beginning, as I blogged multiple times from different angles, in the following posts like [this](https://blog.lextudio.com/what-should-you-check-when-visual-studio-cannot-debug-asp-net-core-projects-4b5db8c5e129), [this](https://blog.lextudio.com/the-horrible-story-of-publishing-net-core-web-apps-for-beginners-6121662dd8c4), and [this](https://blog.lextudio.com/how-visual-studio-launches-iis-express-to-debug-asp-net-core-apps-d7fd3677e3c3).
<!--more-->

So what should a developer do when a related error happens? Going through all the materials and tons of posts on Stack Overflow? That's both a waste of time and also a painful process, because even for a single error code (like 502.5) there are at least four possible causes (while Microsoft documentation only covers one).

I have been thinking about this for a while (since June 2017 maybe), but I wasn't quite familiar with all the necessary details yet, until last month at Microsoft campus. Again, thanks for all the new knowledge I gained from the MVP Summit sessions, and the passions from other participants, so that this year I was able to create [ASP.NET Core Diagnostics for IIS/IIS Express](https://docs.jexusmanager.com/tutorials/ancm-diagnostics.html) on the Hackathon day.

Similar to other diagnostics (like SSL), this new diagnostics tool focus first on data collection, where it checks,

* Whether ASP.NET Core module (ANCM) is installed, and what is the version number.
* Whether Visual C++ 2015 runtime is installed, and what's the version number.
* Whether the application pool is using `No Managed Code`.
* Whether a valid handler is registered.
* What's the web app runtime version.

Of course, whenever possible it also reports typical errors, such as version mismatch between ANCM and the web app runtime. Such should already apply to most 500 and 502.5 scenarios.

I am still trying to improve this tool so that it can cover more cases, and dig up more useful information in the report, so let's see how far we can go from here.

Stay tuned.
