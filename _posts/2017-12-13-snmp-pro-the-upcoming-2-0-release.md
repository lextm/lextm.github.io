---
layout: post
title: "#SNMP Pro, The Upcoming 2.0 Release"
tags: SNMP
permalink: /snmp-pro-the-upcoming-2-0-release-ca4a64943d01
excerpt_separator: <!--more-->
---
{% include image.html
src="bund-xuhui.jpg" caption="Copyright © Lex Li. Bund, Xuhui, Shanghai." width="512px" %}

#SNMP Pro was announced in April 2013 and reached its 1.0 release in February 2014. We will celebrate its fifth birthday soon in early 2018. But before that, let's talk about the upcoming 2.0 release.
<!--more-->

### The Tough Road of Platform Upgrades

We [talked about .NET Standard/.NET Core support](https://blog.lextudio.com/snmp-pro-goes-to-net-core-d3b3ebdc2750) a while ago, and after that we keep silent for a long while. The reason behind was the .NET Core tooling was not as mature as it should be in the first half of 2017. In the meantime, some dependencies we need were not yet upgraded to .NET Standard.

It was some time in September that we were finally ready to carry out all the migration, and in early December we could build and test everything without a problem.

### Changes in 2.0 Release

As stated above, the biggest news is that our product, SharpSnmpPro.Mib, now supports .NET Standard 2.0.

> We decide to stick to .NET Standard 2.0, instead of 1.3, due to some of the dependencies.

Though our 1.x release already supports .NET Framework, Mono, Xamarin and so on, it was not easy for us to ship the assemblies, and building them out is also painful. Thanks to .NET Core tooling and .NET Standard, and now we only ship a single NuGet package.

Except that, we also improved RFC compliance on RFC 1155 and 2578. A few performance and stability issues in the compiler are fixed, and we also moved a few classes to new namespaces.

More information can be found from [this release notes page](https://pro.sharpsnmp.com/support/release-notes.html#releases).

### The Release Day

So, when will you receive it? Our customers would receive them on December 31, 2017. And wish you all a happy new year :)
