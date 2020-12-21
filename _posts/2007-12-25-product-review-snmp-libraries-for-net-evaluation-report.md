---
layout: post
title: "Product Review: SNMP Libraries for .NET Evaluation Report"
tags: SNMP .NET
permalink: /product-review-snmp-libraries-for-net-evaluation-report-e13f25991cad
excerpt_separator: <!--more-->
---
In TCP/IP stack, HTTP, FTP, SMTP, POP, IMAP are popular so you can easily find free libraries to use for those protocols in nearly any language. But to do something about SNMP on .NET the range is quite limited.
<!--more-->

So let's first take a look at what SNMP libraries are available in other languages.

* Net-SNMP (BSD like license) should be the most popular all around the world because it is written in C. So you may find it on any Linux/UNIX system.
* SNMP4j (under Apache 2.0 license) is most popular open source choice for Java developers while commercial products from companies like AdventNet are also quite nice.
* For native C++ developers, SNMP++ (BSD like license) should be a natural choice, although there are tons of commercial products too.

However, while you can find a lot of choices that sometimes drown you in other languages, there is only a few choices you can make on .NET. After googling for a long time, I can only find 5 libraries suitable. They are,

### SNMP++.NET
This one is open source also under BSD license, but I could not compile the source code to a binary file. I am not a master of C++/CLI so I give up. My last attempt was to read the comments inside and found out that there might be some memory leak somewhere because wrapping over SNMP++ is really a hard job.

Because this library is developed with C++/CLI, I am sure it is not Mono friendly. If you need to target Mono, this library may not be a choice.

### PowerSNMP for .NET
Dart provides this excellent product which supports SNMP v1–3. And the demos are fabulous. I was happy when I read the references. Why? Have to say the interfaces are well organised and defined, so every name is natural and meaningful. The only weakness of this library is that it is not Mono compliant (one PInvoke with a few Mono TODOs). An ActiveX edition is also available for native Windows development.

### IP*Works! Secure SNMP for .NET
/n Software has a complete product line for SNMP (full spectrum including Java, C++Builder, Delphi, ActiveX and .NET). However, the weakness is indirect PDU manipulation like creating a PDU from scratch and sending it out. This library has 9 Mono TODOs and supports SNMP v1 to v3. **Notice that /n Software never sells source code to customers.**

> Updated: Lance, I know I can access PDU raw bytes, but it requires me to parse the bytes myself which is not convenient. Why not provide a PDU(Parser) class?

> Updated again: Now #SNMP may be used to parse those bytes.

### SNMP.NET (1.x)
NetToolWorks focuses on .NET library, so its library for SNMP is the only product and quite powerful. The weakness points are,

* Data containing in the response messages are raw string, which means I have to do custom parsing on the data string.
* Only numerical OID/IID can be used because I find no translation utility class inside. So it requires you fully understand the MIB files you use and doing the translation yourself.
* There is no MIB browser demo. If you need to understand the MIB files yourself, no doubt you need to DIY a MIB browser.

I know MIB parsing is optional because you can use numerical OID/IID nearly everywhere, but textual OID/IID support is really a plus because some developers (especially the maintainers) find textual forms more readable.

This library has 8 Mono TODOs and supports SNMP v1 to v3.

> Updated: I don't have time to evaluate the latest version of this library so things may change rapidly.

### AdventNet SNMP API for .NET
AdventNet ports its famous Java library to .NET. And the only issue I see is that there is no SNMP v3 support. And this library is fully Mono 1.2.6 compliant so the best choice if you also target Mono.

### Conclusion
If you are developing a SNMP related product on .NET, you'd better pick up one of the four commercial libraries above to speed up development. The generic requirements you should specify are,

* SNMP v3 support
* Mono compliance
* Low level/PDU interfaces

Do not forget to try the products before buying because all of them are quite expensive (over 1000 USD). If your MIB files cannot be consumed by one library, surely you should choose another.

Also I know there are other open source SNMP libraries for .NET but none of them is feature complete so you cannot easily development a complete SNMP agent or manager out of them.

> Update: After so many months, things change. For example, SNMP.NET is now 3.1 and a few weak points listed here are fixed in this new release. However, I don't have time to initiate a new round of evaluation , so dear readers, please do it yourself and I am sure you can find a good choice.

### Updated Again: The Nstrument .NET Snmp Library
I suddenly came across this library this afternoon and moved by its API design. Impressive. But I did not try it so I don't have more to say.

### #SNMP
What's more? The open source SNMP implementation for .NET and Mono maintained by me is approaching 1.0 release. Now SNMP v1 support is done, and I am working on a MIB browser demo to reveal the power of this library. Like PowerSNMP and Nstrument, #SNMP uses a natural API interface. If you are interested, please have a visit.
