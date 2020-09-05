---
layout: post
title: Things You Should Know About Windows Cache Extension for PHP
tags: IIS .NET ASP.NET
permalink: /things-you-should-know-about-windows-cache-extension-for-php-93b7e013ea4c
excerpt_separator: <!--more-->
---
{% include image.html
src="le-village-olympique.jpg" caption="Copyright © Lex Li. Le Village Olympique, Montreal." width="512px" %}

When Microsoft decided to bring PHP to Windows/IIS, they also created a key component called WinCache, which is a separate open source project. Though its development hasn't been quite active, it is still used by a lot of users.
<!--more-->

You can find [its official page](https://pecl.php.net/package/wincache) (like many other PHP components) on PECL.PHP.net. However, you might not easily find its other useful links. So here I compile a list,

* Its [GitHub repository](https://github.com/php/pecl-caching-wincache).
  > Note that its latest development branch is WinCache_PHP7 , not master.
* Its [own forum](https://forums.iis.net/1164.aspx/1?Windows+Cache+Extension+for+PHP) (though not quite active, its developers do hang out there from time to time).
* Its [downloads section on SourceForge.net](https://sourceforge.net/projects/wincache/files/).

It is now less popular to host anything on SourceForge, but anyway you can find the latest installers (for PHP 7.3) under development folder.

> Keep in mind that Microsoft Azure now recommends you use its Linux based App Service to host Python and PHP sites. As a result, PHP/Python on IIS can lose its necessity.

Update: July 10, 2020. [Microsoft ends PHP on Windows](https://news-web.php.net/php.internals/110907).
