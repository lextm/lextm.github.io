---
layout: post
title: IIS 7 and WSUS on Windows Server x64
tags: IIS
permalink: /iis-7-and-wsus-on-windows-server-x64-1b59da5ac7ee
excerpt_separator: <!--more-->
---
It is common to see people install IIS and WSUS on the same box. However, if the following conditions are met, you may come across a 500.19 that's strange and hard to diagnose on your own.
<!--more-->

1. This server is running Windows Server 2008 or Windows Server 2008 R2 x64 build.
1. There are IIS application pools that allows 32 bit applications.

The error page can be similar to this one,

``` text
Module DynamicCompressionModule
Notification SendResponseHandler StaticFile
Error Code 0x8007007e
Requested URL http://localhost:80/
Physical Path C:\inetpub\wwwroot
Logon Method Anonymous
Logon User Anonymous
```

The root cause is simple. WSUS installer inserts a dynamic compression module into IIS 7 named `xpress`. This asks IIS 7 to load `%windir%\system32\inetsrv\suscomp.dll`.

All works fine for 64 bit application pools till you allow 32 bit applications. In the folder `%windir%\SYSWOW64\inetsrv\` there is not a 32 bit version of `suscomp.dll`, so IIS 7 cannot load this dynamic module and reports 500.19 with error code `0x8007007e`.

``` text
ERROR_MOD_NOT_FOUND winerror.h
# The specified module could not be found.
```

Well, how to resolve it? Depending on your situation, there can be several ways,

* Split your applications, so on this WSUS box, only run 64 bit applications.
* Find a 32 bit copy of `suscomp.dll`, and then copy it to `%windir%\SYSWOW64\inetsrv\`.
* You can also locate the `suscomp.dll` line in `applicationHost.config` and add `bitness64` flag to its `preCondition` field, which effectively prevents the setting from taking effect in 32 bit application pool.

> WSUS should ship such a 32 bit copy with it, but since it does not, you have to own a 32 bit server, and copy the dll over.
