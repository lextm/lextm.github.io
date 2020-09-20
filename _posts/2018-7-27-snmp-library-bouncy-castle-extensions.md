---
layout: post
title: \#SNMP Library, Bouncy Castle Extensions
tags: Visual-Studio .NET SNMP
permalink: /snmp-library-bouncy-castle-extensions-e85333583bb4
excerpt_separator: <!--more-->
---
{% include image.html
src="washington-monument.jpg" caption="Copyright © Lex Li. Washington Monument." width="512px" %}

Microsoft decided to make a few encryption algorithms obsolete in .NET Core. Everyone knows they did this for good. But the crude fact is that many existing things unfortunately depend on such algorithms, whether you like it or not.
<!--more-->

I [documented the missing pieces](https://blog.lextudio.com/misery-around-platformnotsupportedexception-1b890c74a08a) for #SNMP last year, and at that moment I had to throw `PlatformNotSupportedException`.

Some changes just landed on the code base, as Matt Zinkevicius [suggested and helped implement a few AES privacy providers](https://github.com/lextm/sharpsnmplib/pull/81) based on the open source Bouncy Castle project.

Notice that all such algorithms go to a separate NuGet package, named `Lextm.SharpSnmpLib.BouncyCastle`. This reminds you that the execution is purely in managed code, so might be significantly slower than the native counterparts.

I revised the relevant documentation, to make it part of our 10.0.9 release (because it does not require anything other than `Lextm.SharpSnmpLib` 10.0.9 package. [A new article](https://docs.sharpsnmp.com/en/latest/tutorials/aes.html) has been added to document the changes.

Interestingly, I found it pretty easy to write a DES privacy provider based on Bouncy Castle. It should be released in 10.0.10.

Stay tuned.

> Update: Due to a support policy change, Bouncy Castle based extensions have been moved to a separate repo. The NuGet packages are discontinued. More details can be found in [this post](https://blog.lextudio.com/snmp-library-11-0-and-above-96c31e9d11b8).
