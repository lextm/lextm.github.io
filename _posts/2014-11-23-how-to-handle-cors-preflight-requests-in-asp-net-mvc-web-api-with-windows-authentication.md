---
layout: post
title: How to Handle CORS Preflight Requests in ASP.NET MVC/Web API with Windows Authentication
tags: IIS .NET
permalink: /how-to-handle-cors-preflight-requests-in-asp-net-mvc-web-api-with-windows-authentication-f19814cbb558
excerpt_separator: <!--more-->
---

It is interesting that many ASP.NET developers find this something hard to do, so I write the tips out to reveal that starting from IIS 7 Microsoft makes things so much easier.
<!--more-->

### What is CORS Preflight Requests
Well, I don't want to duplicate the details as you can easily find it [here at Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS#Preflighted_requests) and probably other places. So simply speaking, such requests are simply HTTP requests with OPTIONS verb.

### What is The Error
Unfortunately such preflight requests do not contain user credentials, so IIS automatically responds with 401.2 error page if the ASP.NET content is protected by Windows authentication (and other similar authentication methods) and wants to start a challenge. Note that many browsers such as Firefox won't respond the challenge, but simply fails the CORS attempts.

### What is The Fix
Now we know that IIS responds before ASP.NET in most cases, but when fully embracing the integrated pipeline mode we do have a chance to hack.

Remember the fact that [ASP.NET HTTP module](http://www.iis.net/learn/develop/runtime-extensibility/developing-a-module-using-net) (derived from `System.Web.IHttpModule`) can now be loaded directly by IIS?

That means if we hook to `HttpApplication.BeginRequest `event (which triggers before `AuthenticateRequest`), we can return whatever response we like.

So to handle the preflight issue, we simply create such a module, and return 200 response at `BeginRequest` event with the expected headers (about which headers are expected by the web browsers, please refer to the Mozilla article I linked earlier, as it carefully shows even sample requests/responses).

All module development technical details can be found in the above IIS.net article, so I am not going to provide full source code.

### What about IIS 6 or Classic Mode
Windows Server 2003/IIS 6 is dying fast, but I believe there are tons of classic mode users. You can still get things done, but in a much more difficult way, as you will have to use C/C++ to [write an ISAPI module](http://www.iis.net/learn/develop/runtime-extensibility/develop-a-native-cc-module-for-iis) and perform the logic I described above.

### Update on Nov 10, 2017, IIS CORS Module 1.0

Sounds like finally [IIS can support CORS and preflight natively](https://blogs.iis.net/iisteam/introducing-iis-cors-1-0). Check it out.
