---
layout: post
title: The Story About .NET Cross Platform UI Frameworks
tags: Visual-Studio .NET
permalink: /the-story-about-net-cross-platform-ui-frameworks-dd4a9433d0ea
excerpt_separator: <!--more-->
---
> Disclaimer: All contents are based on my personal observation. Please leave a comment if you find anything incorrect, and I will revise it often.

There are tons of UI frameworks to choose from if you are going to develop a cross platform GUI application in .NET. This post tries to list their pros and cons for your reference.
<!--more-->

## Native Bindings
Today we can develop cross platform applications using C#, and share non-UI code easily. This has been the best approach so far, as our projects can explore all native controls and third party controls to achieve full OS integration.

> By saying "full OS integration", I mean our apps have full access to system standard controls, such as buttons, check boxes and so on. More examples are,
>
> * Windows system tray
> * Windows jump lists
> * Windows system themes
> * iOS native scrolling
>
> Most importantly, other apps on the same OS are written using the same set of controls, which allows our apps to blend in naturally, instead of being aliens.
>
> Some app types, such as full screen games, are usually aliens everywhere and people get used to that fact. However, not all apps should go that route.

Below are the native UI frameworks for C# developers,

* Windows: Windows Forms (note 1).
* macOS: Xamarin.Mac (wrapper over Cocoa) (note 2).
* Linux: GTK# (wrapper over GTK+) (note 3).
* iOS: Xamarin.iOS (wrapper over Cocoa Touch).
* Android: Xamarin.Android (wrapper over Android UI).

> A few notes,
>
> Note 1: Windows is a monster that has multiple UI frameworks. Here I only list WinForms as "native". WPF and UWP are actually supported frameworks by Microsoft. Many Windows 10 built-in apps are now written in UWP. So UWP is becoming the next native framework.
>
> Note 2 MonoMac is obsolete.
>
> QtSharp is considered not mature enough.

Most projects choose to use this approach, such as

* [NI LabView](http://www.ni.com/en-us/shop/labview.html) uses Xamarin.iOS on iOS, and Windows Forms (not quite sure) on Windows.
* [Plastic SCM](https://www.plasticscm.com/) uses GTK# on Linux, Xamarin.Mac on macOS, and Windows Forms on Windows.
* [iCircuit](http://icircuitapp.com/) uses Xamarin.Mac on macOS, Xamarin.iOS on iOS, Xamarin.Android on Android, and what Microsoft offers on Windows and Windows Phone.

## Cross Platform Frameworks
But some of us do hope for cross platform UI frameworks. So this post shows what has been attempted. Roughly speaking, they go three approaches,

* Full custom rendering and native control emulating.
* Native on some OS and emulating on others.
* Native control mapping.

### Unity/MonoGame

|          | Comment |
| :------- | :------ |
| Approach | Full custom rendering. |
| Supported Platforms | Desktop and mobile (and more, like game consoles) |
| OS Native Look and Feel | Almost none. |
| Third Party Controls | Some |

If you are OK to fully draw all controls, Unity and MonoGame already supports most of the platforms and you can use them to build cross platform apps.

Of course, that works fine for games, but obviously not all applications. Your apps would not look exactly native, as it cannot use OS native controls.

> Third party add-ons such as [noesisGUI](http://noesisengine.com/) amazingly add the possibility to build UI based on XAML and controls. An impressive approach.

### GTK#

|          | Comment      |
| -------- | -------------|
| Approach | Native on Linux, emulating on others. |
| Supported Platforms | Desktop and mobile (and more, like game consoles) |
| OS Native Look and Feel | Linux only. |
| Third Party Controls | Not too many. |

GTK has been a portable framework to build desktop apps. Thus, its C# binding GTK# enables cross platform apps.

However, you should know that GTK# apps only look native on Linux distributions. Running such apps on macOS or Windows is not optimal, as they look like aliens. For example, GTK# apps do not work quite well with Windows themes. GTK# apps do not work on mobile platforms either.

> MonoDevelop used GTK# on all platforms initially, and gradually (in Xamarin Studio phase) started to utilize xwt to utilize native controls. Its successor Visual Studio for Mac uses both GTK# and Xamarin.Mac bits (I think).

Mono's GTK# wrapper is GTK 2 compatible and not yet upgraded to support GTK 3. [A new repo](https://github.com/GtkSharp/GtkSharp) has been created by a MonoGame maintainer Harry to fill the gaps.

### Windows Forms

|          | Comment      |
| -------- | -------------|
| Approach | Native on Windows, emulating on others. |
| Supported Platforms | Desktop |
| OS Native Look and Feel | Windows only. |
| Third Party Controls | Many on Windows. |

Windows Forms works for Windows (including Windows CE, though no serious interest on that now). Mono has a clone of Windows Forms, so some projects use that to port .NET Framework Windows Forms apps to macOS and Linux. So it sounds like a cross platform framework candidate.

However, Mono's implementation is buggy and a lot of efforts would be required to further enhance it. Thus, some projects (such as [Plastic SCM](http://blog.plasticscm.com/2014/12/native-linux-gui-gtkplastic.html)) initially take this approach and later drop it.

> Mono's WinForms implementation on macOS uses some legacy interfaces and is 32 bit only. So when latest macOS no longer supports 32 bit applications, this approach is a dead end. There was an attempt to port Mono WinForms to 64 bit, but no further news on that.

Recently [Microsoft decided](https://github.com/dotnet/corefx/issues/20325) to port `System.Drawing` to non-Windows platforms, which gives an opportunity to also [port Windows Forms officially to non-Windows platforms](https://github.com/dotnet/corefx/issues/21803). It would be welcome if that port becomes a better alternative than Mono's implementation, but whether third parties (commercial/open source) can catch up is uncertain. Third party controls are fantastic on Windows, but rarely they support other OS. Developers usually find it pretty painful to move Windows Forms apps to non-Windows platforms due to such controls they use.

More importantly the design of Windows Forms is suitable for desktop apps, but may be not for mobile platforms (personal opinion clearly). Xamarin guys initially had [an idea to port Windows Forms to iOS](http://tirania.org/blog/archive/2009/Sep-14.html). They gave that up and instead decided to bind natively to Cocoa Touch.

> Note that Microsoft is going to support Windows Forms on .NET Core 3.0. Officially Microsoft will make it available on Windows, but anyone can attempt to port to other systems.

### WPF/Avalonia/UWP/WinUI

|          | Comment      |
| -------- | -------------|
| Approach | Full Custom Rendering. |
| Supported Platforms | Desktop (and possibly mobile) |
| OS Native Look and Feel | Windows only. |
| Third Party Controls | Many on Windows. |

Mono was trying to port WPF, but that project was not finished due to [lack of resources](http://www.mono-project.com/docs/gui/wpf/). [Avalonia](https://github.com/AvaloniaUI/Avalonia) is another open source project to do the same.

> Neosis GUI mentioned early can also be seen as a WPF clone, and it is much more mature than Avalonia (it even supports Blend in some degree).

Similar to Unity/MonoGame, WPF internally renders everything on its own, so technically speaking it can go cross platform.

> Delphi has a similar framework called FireMonkey, which is already cross platform (both desktop and mobile).

However, this approach has the disadvantages just like Unity/MonoGame, that all controls do not render like native ones.

> Microsoft does ship many themes for WPF apps to look quite good with Windows, but still in certain area the look and feel differs.
>
> FireMonkey met such issues and its developers have tried to resolve that for a long time.
>
> Again (personal opinion) Windows apps should move gradually from Windows Forms/WPF to UWP. So in the near future, UWP would become the "native" solution on Windows.
>
>Note that Microsoft is going to support WPF on .NET Core 3.0. Officially Microsoft will make it available on Windows, but anyone can attempt to port to other systems.

### Xamarin.Forms/Maui

|          | Comment      |
| -------- | -------------|
| Approach | Native control mapping. |
| Supported Platforms | Mobile and desktop. |
| OS Native Look and Feel | Always. |
| Third Party Controls | Growing. |

Xamarin.Forms was invented for mobile platforms initially, and recently starts to expand its landscape to desktop, macOS/WPF/GTK# backends.

Unlike Unity/MonoGame/WPF/Avalonia who performs custom rendering and emulates OS controls, Xamarin.Forms apps pick up native controls at startup, so all you see is purely native. This kind of UI element mapping makes sure your apps are not different from any other apps written natively.

More importantly, Xamarin.Forms also gives you flexibility to embed native controls whenever necessary, and its pages can also be embedded into native apps. So hopefully it is the most flexible option for you.

You should notice that initially it was designed for mobile apps. Currently I am not sure how a typical desktop app (such as Office or Visual Studio) can fit into Xamarin.Forms. Luckily, not everyone of us need to write Office or Visual Studio, so this framework might just serve your needs excellently.

Quite a lot of third party vendors are now offering Xamarin.Forms controls, so that you can build Line-of-Business apps efficiently,

* [ComponentOne](https://www.componentone.com/Xamarin/)
* [Telerik](http://www.telerik.com/xamarin-ui-2)
* [Synfusion](https://www.syncfusion.com/products/xamarin)
* [Infragistics](https://www.infragistics.com/products/xamarin-forms)
* [DevExpress](https://www.devexpress.com/products/xamarin/)

This momentum also makes it appealing to use this framework.

Microsoft just announced [Maui](https://devblogs.microsoft.com/dotnet/introducing-net-multi-platform-app-ui/), a major upgrade of Xamarin.Forms, as a new option to build cross platform applications.

However, due to the Wuhan virus pandemic, its delivery date has been changed from Nov 2020 to Nov 2021.

### Uno Platform

|          | Comment      |
| -------- | -------------|
| Approach | Native control mapping.* |
| Supported Platforms | Mobile, web, and desktop. |
| OS Native Look and Feel | Always.* |
| Third Party Controls | Growing. |

This is [a newest player](https://platform.uno/) in the field (2018 May), which is quite similar to Xamarin.Forms. However, its designers made a few important choices to make the final approach unique,

* Uno Platform chose UWP/WinUI as its starting point, so all basic APIs mirror what Microsoft UWP/WinUI offers.
* It then utilizes Xamarin or WebAssembly to implement those APIs on different operating systems (and the web).
* Its XAML support is complete (while Xamarin.Forms is catching up).
* It also supports native controls, but does not use the "renderers" approach Xamarin.Forms chose.

> Uno does support fully custom rendering upon Skia in its latest release.

The source code is all available on GitHub, and has been quite active ever since.

Starting from Microsoft Ignite 2019, Microsoft WinUI team started to [embrace Uno as their recommended cross platform approach](https://blogs.windows.com/windowsdeveloper/2019/11/04/developer-platform-updates-at-microsoft-ignite-2019/).

Third party control vendor Syncfusion announced its [collaboration](https://www.syncfusion.com/blogs/post/collaboration-syncfusion-uno-platform.aspx) earlier in September 2019.

### xwt/Eto.Forms

|          | Comment      |
| -------- | -------------|
| Approach | Native control mapping. |
| Supported Platforms | Desktop (and possibly mobile). |
| OS Native Look and Feel | Always. |
| Third Party Controls | Not many. |

Both are desktop UI frameworks using Xamarin.Forms approach (native control mapping).

xwt is very old, so personally I think its design might inspired the invention of Xamarin.Forms. Eto.Forms is young, and recently starts to land on mobile platforms.

Whether the two frameworks can grow as mature as Xamarin.Forms is uncertain. They only offer a handful of controls right now, and no sign of third party controls. Whether native control embedding is supported is also unknown.

> **Some Incomplete Information on Qt**
>
> I don't do much Qt programming, so my knowledge of this cross platform framework is limited.
>
> It is also not quite well supported on .NET/Mono due to the following challenges,
> * Its API is C++ based so very hard to wrap in C#/.NET. That's why libraries such as QtSharp cannot map all the functionality.
> * I cannot comment much on its rendering effect, but it seems to use custom rendering.
> * Interestingly that Qt guys do develop their own markup languange called QML.
> * You might use QtSharp to develop a Qt app, and a new project called [Qml.Net](https://github.com/pauldotknopf/Qml.Net) is another option.

> Look for other interesting posts like this one? You can visit [the index page](https://blog.lextudio.com/all-in-one-for-the-legends-of-net-materials-43c374a01433).
