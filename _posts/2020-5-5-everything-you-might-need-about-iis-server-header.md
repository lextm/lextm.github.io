---
layout: post
title: Everything You Might Need About IIS Server Header
tags: Windows IIS nginx Microsoft
excerpt_separator: <!--more-->
---
{% include image.html
src="vancouver-convention-center.jpg" caption="Copyright © Lex Li. Vancouver Convention Center." width="512px" %}

When testing some requests/responses on IIS with a good utility such as Telerik Fiddler, you might discover much more information than using a browser.
<!--more-->

### Most Common Server Header in Responses
So you can see from Figure 1 that IIS returns several HTTP headers in the response and `Server: Microsoft-IIS/10.0` indicates that it comes from an IIS 10 machine.

{% include image.html
src="normal-iis-200-response.png" caption="Figure 1 Normal IIS 200 Response" %}

> Note that IIS 10 actually runs on multiple Windows editions/versions,
>
> * Every Windows 10 editions and versions.
> * Every Windows Server 2016/2019 editions and versions.
> * Several container only Windows Server releases such as 1903.

> Also note that some security scan might indicates revealing your web server type is an issue and you should remove such Server headers. However, it is really not a solid suggestion, because the server type can be detected from other response behaviors, so you cannot really hide that.

Alternative Server Header in Some Error Responses

But can IIS return a different Server header? Sure. If we intentionally send a broken request, we can trigger a completely different response Server header like in Figure 2,

{% include image.html
src="iis-400-error-response.png" caption="Figure 2 IIS 400 Error Response" %}

Spaces in request header names are violations of the HTTP protocol, so any modern web server should reply with an error response. IIS is no difference, but suddenly the Server header says `Server: Microsoft-HTTPAPI/2.0`. Why?

If you are familiar with IIS architecture, you won't be surprised, right? IIS relies on an important Windows component, aka HTTP API or http.sys. That component performs many low level tasks, such as HTTP packet parsing and request queuing for application pools. So if an error is detected, before the request goes to the actual IIS worker process, http.sys handles it and sends an error response directly with its own Server header.

> Note that HTTP API 2.0 is available on multiple Windows editions/versions.

Only a bunch of error responses come from http.sys, so only they carry this Server header. Most error responses still come from IIS worker processes, so they use the other Server header.

### How To Remove/Change Server Headers

Well, there has been far too many previous posts/discussions on how to remove such headers, so I won't try to talk too much on details, but just comment on pros and cons.

#### Option 1: URL rewrite rules

IIS URL rewrite rules can change response headers, so of course they can be used to alter Server headers. But I'd like to remind you such rules are only executed in IIS worker processes, so it is impossible for that approach to work on http.sys error responses.

#### Option 2: Request filtering

IIS 10 finally adds a new setting removeServerHeader in [request filtering section](https://docs.microsoft.com/en-us/iis/configuration/system.webserver/security/requestfiltering/#new-in-iis-100),

``` batch
appcmd.exe set config "Default Web Site" -section:system.webServer/security/requestFiltering /removeServerHeader:true
```

However, keep in mind that it does not apply to http.sys error responses either.

#### Option 3: HTTP API Setting

There is a setting for http.sys to disable Server header, which you can configure via PowerShell

``` powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\HTTP\Parameters" -Name DisableServerHeader -Value 1
```

Then restart the machine to take effect.

After setting this, http.sys won't send Server header any more.

### Conclusion

OK. Now you know using a combination of 1+3 or 2+3 finally the Server headers can be reliably removed.

### Sidenotes

Another reliable way I can think of, is to set up a reverse proxy in front (such as nginx), and then let it modify the responses to use a different Server header. It is widely used, and probably why nginx is so popular.

> Microsoft just announced [its own reverse proxy server based on .NET Core](https://devblogs.microsoft.com/dotnet/introducing-yarp-preview-1/), aka YARP.

You shouldn't use legacy tools such as URLScan any more.

> There seems to be [bugs in this area](https://serverfault.com/questions/1017033/how-to-hide-the-iis-8-5-header-when-response-code-403-7-is-returned).
