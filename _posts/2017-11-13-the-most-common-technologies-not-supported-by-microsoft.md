---
layout: post
title: The Most Common Technologies Not Supported By Microsoft
tags: Visual-Studio .NET
permalink: /the-most-common-technologies-not-supported-by-microsoft-8675c409bde2
excerpt_separator: <!--more-->
---

Microsoft designs its products and technologies for many scenarios, but not for all. Sometimes if you use a certain technology in a wrong way, you won't receive official technical support from Microsoft support team. In such cases, please switch to alternative ways, and that usually saves you tons of hours on the dead end.
<!--more-->

### Server Side Office Automation
You might be quite familiar with Office Automation so that your program triggers Word/Excel/PowerPoint/Visio and so on to manipulate various Office documents in flexible ways. It would work great for desktop applications.

However, if you use the same approach in a web application (such as ASP.NET), then you fall into the category of "server side Office Automation". This specific usage might violate Office end user license agreement, and also fall out of Microsoft's initial design. Thus, you might be able to find a million posts over the internet saying they make server side Office Automation working, but you shouldn't do that anyway.

Microsoft has a [Knowledge Base article](https://support.microsoft.com/kb/257757) for this, so read it and you will find alternatives.

### System.Drawing In Windows Service Or ASP.NET
When Microsoft designed System.Drawing namespace based on GDI+, it was meant to be the power behind Windows Forms, the desktop application framework. Thus, everywhere it requires a user session and its resources so as to draw stuffs correctly.

You can scroll down to [the Remarks section of this page](https://docs.microsoft.com/en-us/dotnet/api/system.drawing?view=netframework-4.7.1) and it says clearly that

> Classes within the System.Drawing namespace are not supported for use within a Windows or ASP.NET service. Attempting to use these classes from within one of these application types may produce unexpected problems, such as diminished service performance and run-time exceptions. For a supported alternative, see [Windows Imaging Components](https://msdn.microsoft.com/library/windows/desktop/ee719654(v=vs.85).aspx).

Note that [System.Drawing.Printing](https://docs.microsoft.com/en-us/dotnet/api/system.drawing.printing?view=netframework-4.7.2) has the same restriction.

### System.Windows.Media In Non-WPF Apps
Similarly, when Microsoft designed [System.Windows.Media namespace](https://docs.microsoft.com/en-us/dotnet/api/system.windows.media?view=netframework-4.7.1), it was meant for WPF apps only,

> Provides types that enable integration of rich media, including drawings, text, and audio/video content in Windows Presentation Foundation (WPF) applications.

If you force to use them in other application types, unexpected things can happen.

### Mapped Drives In IIS
Mapped drives are very special ways to extend file system and they already involve user accounts and permissions when created. The complexity of IIS and the web apps running upon it makes it really difficult to properly support mapped drives, so [Microsoft explicitly states that mapped drives are not supported by IIS at all](https://support.microsoft.com/en-us/help/257174).

But you can always use UNC paths instead.

> Note that if you use a desktop virtualization solution, such as VirtualBox or VMware Workstation, it might map host machine folders to the guest Windows machines to enable easy file sharing. However, such folders might show as mapped drives on the guest machines, which cannot be used on IIS due to the limitation. It is recommended that you use other cross machine sync mechanism, such as Git/Dropbox or others to sync your web contents.
