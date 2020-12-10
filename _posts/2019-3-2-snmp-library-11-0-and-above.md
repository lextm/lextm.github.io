---
layout: post
title: "#SNMP Library, 11.0 and Above"
tags: Visual-Studio .NET SNMP
permalink: /snmp-library-11-0-and-above-96c31e9d11b8
excerpt_separator: <!--more-->
---
{% include image.html
src="winter-branches.jpg" caption="Copyright © Lex Li. Tree branches in a winter night, Montreal." width="512px" %}

I just revised and published [release notes](https://github.com/lextudio/sharpsnmplib/releases) for #SNMP Library 11.0 and 11.1.
<!--more-->

The most significant changes are,

* The core library shrinks to the smallest.
* The extensions (BouncyCastle based providers, pipeline classes) move to a dedicate repo.
* The support policies change accordingly. Only the core library is covered by LeXtudio bugfix services.

All the changes above have been driven by user feedbacks in the past few years.

For example, the pipeline classes were initially designed in 2008/2009, and used for integration testing. Although basic functionality has been added gradually, its quality never reaches a level that can be used for production. Recent issues and pull requests on GitHub about it also indicate how badly its extensibility is. I couldn't find a good way to meet all requirements, and shipping such an implementation to users increases support efforts. By moving such things to the sample repo, developers can simply fork it and use their own implementation instead.

#SNMP Pro edition will be updated soon to adapt to the new releases. Stay tuned.
