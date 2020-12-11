---
layout: post
title: How to Let Android Emulator Access IIS Express
tags: Visual-Studio IIS
permalink: /how-to-let-android-emulator-access-iis-express-f6530a02b1d3
excerpt_separator: <!--more-->
---

> Jexus Manager can be downloaded from https://jexusmanager.com

I bet this question has been asked a million of times, but hope not every blog posts cover all the hidden details. And this blog tries to provide every bits that you might be interested in.
<!--more-->

First tip you should understand is that the Android emulator (a virtual device) has its own networking. So Google defines a magic IP address `10.0.2.2`. If you try to browse to this IP address, the requests would be routed to `127.0.0.1` loopback adapter of the host machine. (Later I will give you proof on the magic routing.)

Such a magic can be quite useful if you host your test web site on IIS and that site monitors all network adapters. One such example is the Default Web Site, whose has one important HTTP binding `*:80:*`. So the Default Web Site would be able to serve all incoming HTTP requests at port 80 on all network adapters, including requests to `http://10.0.2.2(:80)` from the Android emulator.
However, this magic won't work for IIS Express by default, if your site has only an HTTP binding like `*: 61902:localhost`”`, and you might see an error like this, ("Bad Request - Invalid Hostname", "HTTP Error 400. The request hostname is invalid.")

{% include image.html
src="emulator-400.png" caption="Figure 1: Android emulator 400 error page." %}

The reason behind is pretty obvious, that IIS Express only checks incoming requests at port 61902, whose HTTP Host header matches "localhost". The request from Android does not have "localhost" as Host header (guess what's value then?), so it would be dropped, and a 400 error page is returned.

{% include image.html
src="iis-express-bindings.png" caption="Figure 2: Default site bindings on IIS Express." %}

You might wonder if we can get rid of the error by modifying IIS Express configuration file. You can try it out in Jexus Manager,

{% include image.html
src="jexus-manager-before-editing.png" caption="Figure 3: Site binding before editing." %}

### Simplest Fix
{% include image.html
src="jexus-manager-custom-host.png" caption="Figure 4: 127.0.0.1 as host name." %}

If you decide to change from "localhost" to `127.0.0.1`, congratulation that you found the simplest fix. It seems that the Android magic sets Host header to `127.0.0.1` so that IIS Express can process the requests in this way.

### More Complicated Fix
What happens if you happen to decide to leave the field blank?

{% include image.html
src="jexus-manager-blank-host.png" caption="Figure 5: Blank host name." %}

Sadly you will see the issue remains (unless you run Jexus Manager as administrator and then start the web site from it). Why?

Full IIS runs as a service, so when it attempts to activate a site binding, all permissions are met. The same applies to running Jexus Manager as administrator and starting the web site from it. But when Jexus Manager is not run as administrator, the IIS Express worker process it launches for this web site would lack the permissions to activate the site binding. The result is that you see the site has started, but incoming requests won't work.

Microsoft of course has a solution for this, which is called URL reservation. An administrator can create a reservation and grant certain users permissions to use it. Let's see how it works in Jexus Manager.

{% include image.html
src="http-api.png" caption="Figure 6: Open HTTP API page in Jexus Manager." %}

On a server node, you can now open a brand new HTTP API page,

{% include image.html
src="reserved-urls.png" caption="Figure 7: Reserved URLs in Jexus Manager." %}

OK, all existing reserved URLs are displayed in the list, and you can use Add/Remove buttons to manipulate the list.

By adding a new reservation of `http://*:61902`, all necessary permissions are granted to everyone on this machine (the meaning behind `D:(A;;GX;;;WD)`). And then when a non-administrator user launches the worker process for this web site, requests to port 61902 would be well served.

We can see that indeed now requests to `http://10.0.2.2:61902` are being served by IIS Express.

OK. Now let's see how IIS Express log file tells the truth of the incoming requests.
``` text
#Software: Microsoft Internet Information Services 10.0
#Version: 1.0
#Date: 2017–07–15 02:46:56
#Fields: date time s-ip cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Referer) sc-status sc-substatus sc-win32-status time-taken
2017–07–15 02:46:56 127.0.0.1 GET / — 61902–127.0.0.1 Mozilla/5.0+(Linux;+Android+6.0;+sdk_google_phone_armv7+Build/MASTER;+wv)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Version/4.0+Chrome/44.0.2403.119+Mobile+Safari/537.36–500 19 21 21
2017–07–15 02:46:58 127.0.0.1 GET /favicon.ico — 61902–127.0.0.1 Mozilla/5.0+(Linux;+Android+6.0;+sdk_google_phone_armv7+Build/MASTER;+wv)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Version/4.0+Chrome/44.0.2403.119+Mobile+Safari/537.36 http://10.0.2.2:61902/ 200 0 21 2
```

See. IIS Express thinks the client IP (c-ip) is `127.0.0.1` for Android requests, while it serves them at `127.0.0.1` of course. The user agent string is pretty ugly also. Interestingly, the request to `/gavicon.ico` recorded a referer of `http://10.0.2.2:61902`, which proves clearly the request came from the Android web browser.

To sum up, to resolve the 400 error, you have several options,

* Run Jexus Manager and change the site binding to use `127.0.0.1` as host name.
* Run Jexus Manager as administrator, and then start the web site.
* Run Jexus Manager normally, and create a URL reservation (elevated on demand), and then start the web site.

Still just a few clicks, and there is no need to jump to command line interface to type all the commands.
