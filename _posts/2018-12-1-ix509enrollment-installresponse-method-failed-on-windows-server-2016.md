---
layout: post
title: Migrating Jexus Manager to .NET Core 3.0
tags: Visual-Studio .NET IIS
permalink: /ix509enrollment-installresponse-method-failed-on-windows-server-2016-fd1a7c1a7b33
excerpt_separator: <!--more-->
---
This is only a quick post on a recent issue I met, that [the COM API of `Certenroll.h`](https://docs.microsoft.com/en-us/windows/desktop/api/certenroll/nf-certenroll-ix509enrollment-installresponse) failed miserably on Windows Server 2016, with an error code of "0x80071771" (The specified file could not be encrypted).
<!--more-->

The error code itself makes no sense, and the same code runs fine on Windows Server 2008 R2 (didn't test other Windows versions).

I didn't plan to open a bug report to Microsoft yet, but was able to find an alternative way to install the response from CA, by calling `certreq` command line utility via `Process.Start` . I know that looks ugly, but `certreq` works flawlessly with the same response data, which indicates that the error comes from `Certenroll.h` API.

You can learn [more about `certreq`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/certreq_1) from Microsoft.
