---
layout: post
title: History of PHP Manager for IIS
tags: PHP IIS
permalink: /history-of-php-manager-for-iis-7e29bd9828f0
excerpt_separator: <!--more-->
---
{% include image.html
src="wellington-ann.jpg" caption="Copyright © Lex Li. Crossroad on rue Wellington, Montreal." width="512px" %}

> Ironically [I suggested people to avoid PHP Manager](https://blog.lextudio.com/why-you-should-forget-php-manager-for-iis-953fae81b05b), but later took over the project.
<!--more-->

### Overview of PHP on IIS

PHP has been a popular programming platform for web applications. It has been integrated to IIS a long time ago by the PHP community.

However, the initial work used IIS ISAPI to integrate, and had stability issues.

In 2006, Microsoft announced [a new initiative](http://mvolo.com/fastcgi-for-iis-60-is-released-on-download-center/) to support PHP on IIS officially via FastCGI protocol.

It took about a year for that to finish and then the ISAPI module became obsolete.

The same technology was later used in Windows Azure (renamed to Microsoft Azure today) to support PHP web apps.

### Initial Releases from Ruslan Yakushev

From the [code commit history](http://ruslany.net/tag/php-manager/) we can see that Ruslan made the first commit on August 9, 2010. However, the initial code seems to be a little bit older than that.

This project is an IIS Manager extension (plus PowerShell snapin) to better support PHP configuration system via IIS Manager experience. So it was quite popular.

Ruslan worked for Microsoft, and was in IIS team (product managr) before he later moved to other positions.

The last commit he made was on December 13, 2013.

The releases from him were 1.0.x-1.2.1.

### Chaos in The Coming Years

There were many factors that made the situation tough for PHP Manager users,

* PHP started to evolve fast. The 7.x releases came quickly and things started to age.
* Windows started to release more often. New IIS releases (8.x and 10.x) were not quite well supported.
* .NET Framework 4.x introduced breaking changes that also affected the old installers.
* CodePlex shutdown took effect a few months ago, which closed down the home page for this project.
* Other noticeable changes.

There were several attempts to help out the users, but they only provided temporary solutions, and [none aimed to revive the project](https://github.com/phpmanager/phpmanager/issues/1) and bring it to a healthy state.

Noticeably, there were installers for "1.3.0" and "1.4.0" from SkyDrive/OneDrive, but the actual source code was not available.

There was an installer for 1.5.0, but it only aims for IIS 10.

### New Repository for 2.0 Release And Above

To fully take over the project and make it healthy again, I did the following,

* Fork the code on GitHub.
* Set up AppVeyor to compile the code and generate new installers.
* Migrate the documentation to the new homepage.
* Fix known issues.
* Test on all supported platforms.

Currently the 2.0 Beta 1 release is still being worked on. An official release is expected by the end of August, 2018.

So that's the whole story, and now PHP Manager for IIS has [a new homepage](http://www.phpmanager.xyz/).

Stay tuned.
