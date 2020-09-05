---
layout: post
title: The Horrible Facts on Visual Studio Setup Projects
tags: Microsoft Visual-Studio Windows-Installer
permalink: /the-horrible-facts-on-visual-studio-setup-projects-45e66fe86d8d
excerpt_separator: <!--more-->
---
{% include image.html
src="seattle-public-library.jpg" caption="Copyright © Lex Li. Seattle Public Library." width="512px" %}

Microsoft Windows requires software to be packed as installers so as to install easily for end users. The evolution of Windows leads to various installer technologies to be invented along the way, and around year 2000 [MSI](https://en.wikipedia.org/wiki/Windows_Installer) became the standard approach.
<!--more-->

Therefore, .NET based applications should be deployed as MSI packages if possible, and Microsoft intentionally pushed developers to that path. Visual Studio Setup Projects were introduced as a result, and developers can create such projects to compile their binaries (and various settings) into MSI packages.

However, such an approach has its own flaws from the very beginning,

* This project type was introduced long before MSBuild was introduced, so its proprietary format is hard to understand and/or edit by hand.
* Many behaviors of this project type (such as automatic dependency detection) are in the black box.
* Documentation on this project type is also harsh, compared to C#/.NET.
* This project type lacks many useful features, compared to partner products (such as InstallShield, which Microsoft ships a "Limited Edition" in VS).
* More importantly, Microsoft freed WiX as an open source project on SourceForge, which is a much cleaner approach to create MSI based installers.

In 2011, Microsoft announced the death of this project type in [a blog post](https://devblogs.microsoft.com/buckh/visual-studio-setup-projects-vdproj-will-not-ship-with-future-versions-of-vs/) and confirmed that WiX is a more superior option to go with (even VS itself uses WiX). However, probably some users still preferred the legacy one, so Microsoft kept it as [an extension on the marketplace](https://marketplace.visualstudio.com/items?itemName=UnniRavindranathan-MSFT.MicrosoftVisualStudio2013InstallerProjects).

In general, the code base behind was almost the same and not quite updated for new platforms such as .NET Core (some bugs were fixed, but clearly not more).

As a result of this ambiguous move, till today we can still see tons of questions on sites such as Stack Overflow, asking why this project type does not work quite well for them.

Well, well, let it die please. You really shouldn't expect too much from a legacy tooling revived only under some demands. WiX, InstallAware, and other similar tools can serve you well.
