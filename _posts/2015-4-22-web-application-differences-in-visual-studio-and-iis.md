---
layout: post
title: Web Application Differences in Visual Studio and IIS
tags: IIS
permalink: /web-application-differences-in-visual-studio-and-iis-60fec7e311b3
excerpt_separator: <!--more-->
---

Beginners of web development often find it strange to see their ASP.NET applications run differently in VS and on IIS. Well, they simply expect the same code base yields the same results, but what facts do they take for granted?

## Web Server Differences
Do you know that old versions of Visual Studio uses ASP.NET Development Server? Do you know that Visual Studio 2010 and above bundles IIS Express? The two are the web servers behind the scene. You can check the project settings to see which one is used in your current web project, and learn more about the settings.

IIS on the other hand, is a completely different web server. It does not only provide more features, but also executes your code in a different way.

## Code Execution Details
Every line of code runs under an account. This principle applies to desktop applications, as well as web applications.

### In User Session
So when you use Visual Studio to debug your web application, which account is used to execute your code? Both ASP.NET Development Server and IIS Express use **your account**. Yes, your account! That means in most of the cases,

* All the permissions granted to your account guarantee the program to run in the way you want.
* Most importantly, the application runs in your logon session, so even if you launch Notepad via `Process.Start` in your server side code, you can see Notepad opened.
* Settings of your account are used, such as proxy, screen resolution, printers, and others. So the application might connect to external network freely, can draw something huge, and print out documents properly.
* If certificates are involved, then the web apps read your account's certificates stores.

### In System Session
What about IIS then?

First, which user account is used? What if IIS uses your account to run the code? Well, that often is horrible (consider the fact that most web developers are local administrators). So the designers of IIS (starting from IIS 6) choose a concept called application pool identities to manage the accounts.

> Any web application hosted on IIS must be configured to be associated with an application pool. And that application pool's pool identity (configurable) is then used to execute the code of that web application.
>
> IIS 6 by default uses Network Service, a local account that has only a limited set of permissions, and IIS 7 starts to introduce AppPoolIdentity, which is even more restricted.

Second, in which session does your application run? Another significant difference is that all worker processes (hosting your application) run in system session (session 0), and are completely isolated from other processes running in user sessions.

Third, what resources can be used? Your web application on IIS can only access the resources/settings assigned to the application pool identity, so critical settings (like proxy/screen resolution/printers) can be completely different. Noticeably, the web app can only access the machine's certificate stores.

The security model of IIS (like described above), remediates many common security issues, but impacts a lot on development. With a restricted pool identity, many .NET APIs will give you different results (in many cases, weird exceptions and errors). However, if you embrace the facts above and start to learn about the pool identities, you can easily find out why those differences come and how to fix your code.

## Crazy Examples
* Pop a dialog (open file dialog and so on) in your web app. That of course works fine with Visual Studio/IIS Express, as the dialog shows in your logon session and you can see it. But once on IIS, the dialog is in session 0 on the server, so it is invisible at browser side or when you log on to the server. The dialog prompt will hang the thread opening it, and you have no magic to click on it.

* Run a command line utility or Notepad. If you attempt to do with Visual Studio/IIS Express, the utility or Notepad runs in your logon session and you can do whatever. But again once on server side, the utility or Notepad would be invisible as it is launched in session 0.

* Run printing. Printing and other similar operations require your logon session and the relevant resources, so such code would work flawlessly with Visual Studio/IIS Express. However, if on IIS and in session 0, such code can simply break, as the resources can be restricted or completely missing.

> Note that even Office server side automation can work flawlessly on IIS Express, but it is not supported at all on IIS.

## Extra Note
Most of the tips also apply to Windows service applications, which run under a preconfigured service account instead of the developer's account.

Process bitness (32 bit or 64 bit) can be another important factor beginners tend to neglect, but your web app on IIS Express and IIS can run in different bitness, and consequently consume different resources (database drivers, file system, and registry keys). So keep that in mind while you troubleshoot an issue.

As a personal blog post, this won't capture all the differences. Thus, keep in mind that the two situations differ in many ways (major/minor) and revel in your own troubleshooting.
