---
layout: post
title: The Rough History of .NET Framework Reference Source
tags: .NET Visual Studio
excerpt_separator: <!--more-->
---

> Again this post comes from [a Stack Overflow thread](https://stackoverflow.com/questions/65278997/is-it-somehow-possible-to-see-unit-tests-which-covers-microsoft-referencesource).

.NET 5 was released a few weeks ago, and 2020 is coming to an end. So now let's talk about the stories around .NET Framework Reference Source, which almost always stays in the shadows.
<!--more-->

### The Closed Source Start
.NET Framework was a closed source project from its very beginning, but that doesn't mean Microsoft never shared the source code out.

Under various programs Microsoft shared code base of Windows and .NET Framework with selected partners so that some very interesting products could be developed upon it. One such example is [Grasshopper by Mainsoft](https://www.zdnet.com/article/grasshopper-hops-code-from-visual-studio-net-to-linux-for-free/), which allows .NET Framework based applications to be ported to J2EE platform and then run in a cross platform way.

> Notice this kind of source code sharing programs is also one of the ways that Windows source code leaked out in the past.

Many Microsoft partners don't have the luxury to see the code, so they have to rely on decompilers (.NET Reflector for instance) to peek into the assemblies. For nasty bugs that Microsoft didn't fix in time, they could utilize .NET reflection to touch the private fields and built workarounds.

### The Reference Source Release
Java source code became publicly available under an open source license in 2006 when SUN decided to push it to the next level. That decision allows Java to help boost big data tools like Hadoop/Spark as well as mobile platforms like Android.

Microsoft's attitude against open source at that time made it to follow SUN's steps to open source .NET Framework, but in 2007 Scott Guthrie and his team decided to take an important step by [publishing some of .NET Framework source code](https://weblogs.asp.net/scottgu/releasing-the-source-code-for-the-net-framework-libraries) under a special license called Microsoft Reference License (MS-RL). The actual code was made publicly available in 2008.

Since the goal of this .NET Framework Reference Source program was to help the developers debug into part of .NET Framework, the released source files were far from complete. Resource files/project files were intentionally missing, so that no one wouldn't be able to rebuild the assemblies from the files.

> Note that MS-RL is not an open source license, so open source projects like Mono cannot make use of any of the code.

> Also note that Microsoft did release other kinds of source code around .NET Framework,
>
> * [Rotor](https://en.wikipedia.org/wiki/Shared_Source_Common_Language_Infrastructure), a special CLI implementation for educational use, under a non-open-source license.
> * [WiX](https://wixtoolset.org/), a set of MSI tools built on .NET Framework, under an open source license and hosted on SourceForge.net.

### Open Source Embraced
Through years of relationship with open source communities, Microsoft started to transform itself, especially under the leadership of Satya Nadella. So when Microsoft finally decided to open source the .NET platform in 2014, it was no longer a big surprise.

Though the initiative was to create a brand new NET platform (officially named .NET Core) to replace .NET Framework, Microsoft understood that it takes time to get to the point of unification. So in the first phase, .NET Standard was created to ensure compatibility among all major .NET implementations at that time, .NET Core/.NET Framework and Mono.

Mono built everything from scratch, so compatibility with .NET Core and .NET Framework was always a problem. Integrating .NET Core source code was too big a challenge at the beginning, but doing the same with .NET Framework source code seemed to be easier. To assist, Microsoft changed the license covered the reference source to be MIT (what you see today) and released more and more files.

### The Future is .NET 5/6
.NET 5 was just released, which finally finished the major tasks of merging Mono and .NET Core. In the coming months, the technologies that were once powered by Mono (Xamarin and Unity for example) are going to migrate to .NET 6.

Therefore, both Mono and .NET Framework Reference Source are no longer that important in the ecosystem as they accomplished the core missions assigned. Of course they might stay for a little longer to serve the migration to .NET 6, but you should know where your precious time should be spent.

> Look for other interesting posts like this one? You can visit [the index page](https://blog.lextudio.com/all-in-one-for-the-legends-of-net-materials-43c374a01433).
