---
layout: post
title: "Self Hosting Series: Why Cannot My Web Server (IIS) Be Reached from Anywhere Else?"
tags: IIS
permalink: /self-hosting-series-why-cannot-my-web-server-iis-be-reached-from-anywhere-else-8da0e0b252ef
excerpt_separator: <!--more-->
---

Web servers have become so ordinary when we have so many sucessful web applications, such as WordPress and so on, and it seems that more and more people would like to do self hosting instead of using a service provider. That's alright. However, what you should learn before hosting a web application on your own machine(s)? What kind of technologies you need to learn? These series of posts aim to provide you some hints on what you should know and try out.
<!--more-->

### The Assumptions
Before we move on, let me make some assumptions about your technical skills. If you find yourself difficult to understand what I mean below, try to grab a friend to help you out instead of wasting your own time for nothing.

* You should have basic idea of what is Internet and TCP/IP. This should be covered by computer networking courses at college or great books in this area.
* You should have basic idea of the devices you are going to use. Make sure you know how to find the manual or support for the device you own.
* You should have basic idea of the operating system and the web server you use.

### The Diagram
{% include image.html
src="web-server-network.png" caption="Figure 1: Web server network." %}

OK, above is a much simplified diagram of a corporate or home network, which only aims to show what are the important elements you are going to touch.

### The Web Server
The web server is not something of rocket science. Generally speaking, it is an agent that takes the HTTP requests from the web browser you use, and responds with proper HTTP responses. Thus, after you install the web server and web application, the first thing you are going to do is to launch a web browser on the server itself to test out the web site. In this way, all possible barriers are avoided. If the local browser shows everything good, you hit the first milestone.

If there is anything wrong, please check the following,

* Whether you have set up the web server correctly. Review the service status and server logs.
* Whether the web application runs fine. Review the application status and logs.
* Whether the browser uses a proxy setting that points to a proxy/gateway. If so, disable the proxy setting so let the browser talks to the server itself directly.
* Whether the site binding is correct. Otherwise, requests won't be dispatched to the site by http.sys.

### The Internal Client
An internal client means a machine sitting in the same network of the web server. In the simplest setup, a web browser on such a client should connect directly to the web server (via switches or similar L2 devices). Use this internal browser to test your web site, and if nothing is wrong you hit the second milestone.

If failures occur, please check the following,

* Whether the port on the server is opened (port 80 and 443 are common ports that should be opened). For Windows, the built-in Windows Firewall usually blocks such ports and you need to manually configure it. For other OS, check the administration documents.
* Whether the internal browser uses a proxy server. Check if the proxy can be disabled, or if the proxy works as expected.
* Whether the site binding is correct. Otherwise, requests won't be dispatched to the site by http.sys.

To troubleshoot the link, we usually install network sniffers (such as Wireshark) on both the client and server side. By capturing network packets at the same time, we can see how the requests/responses go, and most the connection issues can be analyzed in this way.

### The External Client
An external client sits far far away from your web server, and it oftern connects to your server via the Internet first, and then passes through a router/gateway (L3 devices usually). If you already have internal clients working fine, it is time to apply the same tricks to external clients. If everything works for external guys, you hit the final milestone.

Common causes of failures here are,

* Your router/gateway setting is wrong and packets are dropped.
* The ISP of your external networking blocks the packets.
* The external client uses an ISP connection that blocks the packets. This is very common so don't treat it as a rare case.

To troubleshoot such issues, you always need to capture network packets. Note that this time you need to capture packets at all possible locations (server, client, and routers if possible). The more you learn about the connection, the easier you determine the cause.

Happy troubleshooting!
