---
layout: post
title: How to Write Add-ins of Visual Studio for Mac
tags: Visual-Studio
permalink: /how-to-write-add-ins-of-visual-studio-for-mac-ee6113db5ddf
excerpt_separator: <!--more-->
---
I just shipped [a test build](https://github.com/xunit/xamarinstudio.xunit/releases/tag/v0.7.0) of xUnit.net add-in at GitHub, which enables Visual Studio for Mac to run xUnit unit test cases.

So what are the steps to build such an add-in? Below I try to show some technical details.
<!--more-->

### Visual Studio for Mac
Microsoft announced this new IDE last year at Connect 2016 keynote, which is a surprise for many developers. But if we check this IDE further, easily we can see it is not a surprise at all.

* This IDE is based on MonoDevelop/Xamarin Studio, which has a very long history already.
* It reuses many useful ingredients from Visual Studio itself, such as the editor experience.

### Extension Authoring
Previously to develop extensions for MonoDevelop/Xamarin Studio, we need to target the 5.x or 6.x profiles of MonoDevelop core binaries. That's why for [xUnit.net add-in](https://github.com/xunit/xamarinstudio.xunit) there are two branches, 5.0 and 6.0.

The core assemblies have significant differences in editor related APIs, due to the big upgrade in Xamarin Studio 6.x. And now again, Visual Studio for Mac (based on MonoDevelop 7.x) requires a new branch.

So to create a new add-in, you can always get start from [the official guide for Xamarin Studio](https://developer.xamarin.com/guides/cross-platform/xamarin-studio/customizing-ide/extending_xamarin_studio_with_addins/),
Extending Xamarin Studio with Add-Ins

Replace all texts of "Xamarin Studio" with "Visual Studio for Mac", and you should be able to get Addin Maker (>=1.3.4) installed and a sample add-in created.

> Note that MonoDevelop.Addins NuGet package should be upgraded to 0.3.9 and above, so that debugging add-ins can be enabled.

Finally to pack up your add-in and share with others, go to the assembly output folder to locate the add-in assembly (`MonoDevelop.XUnit.dll` for example), and call `vstool.exe` utility

``` bash
mono /Applications/Visual\ Studio.app/Contents/Resources/lib/monodevelop/bin/vstool.exe setup pack MonoDevelop.XUnit.dll
```

Then a `.mpack` file would be generated and everyone can manually add it to Visual Studio for Mac once you share it publicly.

> Note that once the add-in repository for Visual Studio for Mac is alive, you don't need to share `.mpack` file in this way.

Now Microsoft/Xamarin has opened up the addin feed for Visual Studio for Mac, so as publisher you should use http://addins.monodevelop.com to publish your addin. Users then use the integrated Extension Manager to search and install.
