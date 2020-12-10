---
layout: post
title: ! #SNMP Library, Gaps Filled in SNMP v3 Support
tags: Visual-Studio .NET SNMP
permalink: /snmp-library-gaps-filled-in-snmp-v3-support-3d05774d06b0
excerpt_separator: <!--more-->
---
{% include image.html
src="four-seasons-boston.jpg" caption="Copyright © Lex Li. Four Seasons, Boston" width="512px" %}

If you have been using #SNMP Library for a while, you should know that our SNMP v3 support has significant gaps, that only DES and AES 128 are supported as privacy protocols.
<!--more-->

It is not a secret that the original v3 support was borrowed from SNMP#NET project, as both share the same open source license. At that time, I only ported DES and AES 128 as experiments.

Over the weekend, I published [the 10.0.6 release](https://www.nuget.org/packages/Lextm.SharpSnmpLib/), which finally ships AES 192/256 and TripleDES, as alternative privacy protocols.

SNMP#NET is now a project on the verge of being abandoned, as

* Its last official release by Milan Sinadinovic was 0.9.4 on April 14, 2014 (4 years ago).
* Qinxiao Ren created a fork and released 0.9.5 on March 1, 2017. He/she also released the corresponding NuGet packages on NuGet.org.

Not sure if there would be any new release of that project, so it is time to fill the gaps and make #SNMP Library a full option for SNMP#NET users to migrate to (if they like).

> Not to mention #SNMP Pro provides more features for enterprise users. More information can be found at [here](https://pro.sharpsnmp.com).
