---
layout: post
title: Everything Good And Horrible About PowerShell Gallery
tags: PowerShell
permalink: /everything-good-and-horrible-about-powershell-gallery-a2814e1a8ab6
excerpt_separator: <!--more-->
---
{% include image.html
src="night-street.jpg" caption="Copyright © Lex Li. Street at night, Montreal." width="512px" %}

Microsoft introduced PowerShell Gallery Preview in July 2015, and announced its Public release on February 25, 2016. The idea was cool that everyone can publish useful PowerShell modules/scripts there and later install on target machines easily.
<!--more-->

I became a happy user just recently, learning that I could dump the vswhere command line utility and switched to its PowerShell equivalent by calling `Install-Module VSSetup -Scope CurrentUser -Force`. More information can be found [here](https://github.com/Microsoft/vssetup.powershell).

Definitely this inspired me to publish some useful module, like [the one Ruslan Yakushev created](https://www.phpmanager.xyz/tutorials/command-line.html) as part of PHP Manager for IIS. However, horrible things started to happen and it took me days to pass them. So the remaining of this post would focus on that.

### Stable/Preview Sites
{% include image.html
src="stable-site.png" caption="Figure 1: PowerShell Gallery." %}

You probably landed on the same site like I did from a search engine, and were attracted by the "Try the Preview" banner.

{% include image.html
src="preview-site.png" caption="Figure 2: PowerSheel Gallery preview." %}

So I went ahead and switched to the Preview site, and click the "Sign in" link. Successfully an account was created but I found that I could not receive the confirmation email. Sigh, I tried several times and then wrote a mail to the support guys.

It took hours to get a reply from them with a link to confirm my mail box, but anyway I should be able to publish the module, right?

### PowerShell Snapin vs. PowerShell Module

At the beginning of PowerShell, custom cmdlets came from extensions called snapins. For example, once PHP Manager for IIS is installed, the snapin assembly is installed to GAC, and then can be loaded by PowerShell via [a single command](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/add-pssnapin?view=powershell-5.1) `Add-PsSnapin PHPManagerSnapin`.

Should I do any conversion to make the existing snapin a new PowerShell module? Internet gave me multiple answers and many were simply misleading. It turns out that a snapin assembly with custom cmdlets can directly be used as [a PowerShell module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/import-module?view=powershell-6), if I simply use `Import-Module`. This part is not quite relevant to publishing a module, but you should know how to test your module assembly locally via `Import-Module`.

### Publishing

Well, I could not publish the assembly directly, as the gallery requires me to provide a manifest file. It is really bad that you cannot find a good enough example somewhere, and some blog posts might contain an example of the old syntax.

So lastly I had to use the [Microsoft example](https://github.com/Microsoft/vssetup.powershell/blob/develop/src/VSSetup.PowerShell/VSSetup.psd1), as well as the [manifest definition](https://docs.microsoft.com/en-us/powershell/gallery/concepts/item-manifest-affecting-ui). Note that the table is formatted badly, as it even contains some attributes you cannot set in manifest. Those can be set via `Publish-Module` command line.

With the necessary assemblies and manifest in the same folder, I thought that I could then publish it. Well, it did not accept my API key. What?

### Updating PowerShellGet

By default, Windows ships with an old version of PowerShellGet, which provides the `Publish-Module` command, so [this module must be updated](https://docs.microsoft.com/en-us/powershell/gallery/installing-psget) so as to recognize the API key from latest gallery site. 
It took a while to do this, and then the new module was published after a very long time.

But that's unfortunately not the end yet. Right now, I am still waiting for the site to show the module I uploaded.

Will update this post, once the horrible wait is over.
