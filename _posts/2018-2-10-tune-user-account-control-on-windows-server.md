---
layout: post
title: How to Tune User Account Control on Windows Server
tags: Windows
permalink: /tune-user-account-control-on-windows-server-a96204c4ebc3
excerpt_separator: <!--more-->
---
It has been a while that I use a Windows Server virtual machine in Azure for all my Windows related testing. I love this approach, so that I can stay comfortably on a MacBook with only virtualized Linux locally.

Microsoft seems to make some changes to suppress UAC prompts for administrators (which makes great sense for real world administrators). However, that's a pain for me to see everything elevated as administrator by default, because I expect it to work more like Windows 10.
<!--more-->

Google does not return much information on how to configure UAC the way I like, so I have to do some digging on my own, and find out the following.

* Search and open Local Security Policy from Start menu.
* Expand the policy tree in the left panel to locate `Security Settings | Local Policies | Security Options`.
* In the right panel, find "User Account Control: Admin Approval Mode for Built-in Administrator account".

The default value should be "Disabled", which I can easily toggle to "Enabled". Then a reboot of the machine can please me with everything in place.

{% include image.html
src="user-account-control.png" caption="Figure 1: User Account Control policies." %}
