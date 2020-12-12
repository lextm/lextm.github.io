---
layout: post
title: "Port Already In Use, Then Who Uses It?"
tags: IIS
permalink: /port-already-in-use-then-who-uses-it-69c133a456ab
excerpt_separator: <!--more-->
---

It is normal that when you try to run an application on Windows and see "port already in use" error message reported, if this application tries to monitor a TCP or UDP port.
<!--more-->

Windows itself has components that monitor famous ports. For example,

* IIS monitors port 80 and 443 on TCP (for HTTP and HTTPS)
* FTP services monitors port 21.
* SNMP services monitors port 161.

Besides, many popular Windows applications (Skype, FlashGet and so on) might also monitor the ports they like.

So for beginners it is puzzling to see which process uses a certain port. But in fact, to locate the process, you only need two simple steps.

``` bash
C:\Program Files\Microsoft Visual Studio 9.0\VC>netstat -aon | findstr 161
UDP 0.0.0.0:161 *:* 1620
UDP [::]:161 *:* 1620
```

The command netstat outputs a table with four columns: protocol type (TCP or UDP), source endpoint (IP address:port number), destination endpoint, and process ID. So we know now 1620 is the PID of the process which monitors port 161.

``` bash
C:\Program Files\Microsoft Visual Studio 9.0\VC>tasklist /fi "PID eq 1620"
Image Name PID Session Name Session# Mem Usage
========================= ======== ================ =========== ============
snmp.exe 1620 Services 0 1,180 K
```

The command tasklist immediately tells what is the process name for 1620. Based on Internet search, you can soon know it is Windows SNMP services.

### Update:
Things like reserved URLs in HTTP API and excluded port range settings can also prevent a port from being used. The following commands can be used to reveal them,

``` bash
netsh http show urlacl
netsh int <ipv4|ipv6> show excludedportrange [protocol=]tcp|udp [[store=]active|persistent]
```
