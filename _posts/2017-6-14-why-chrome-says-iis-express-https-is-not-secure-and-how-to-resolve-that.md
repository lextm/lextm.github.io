---
layout: post
title: Why Chrome Says IIS Express HTTPS Is Not Secure And How to Resolve That
tags: Visual-Studio IIS
permalink: /why-chrome-says-iis-express-https-is-not-secure-and-how-to-resolve-that-d906a183f0
excerpt_separator: <!--more-->
---

> Jexus Manager can be downloaded from https://www.jexusmanager.com

Recently Google updates Chrome to warn on certificates, who only contains CN (Common Name) but no corresponding SAN (Service Alternative Name). This leads to the following error message if you try to test out HTTPS pages on IIS Express by using the default certificate,

{% include image.html
src="not-secure.png" caption="Figure 1: Chrome error page." %}

> **Your connection is not private**
> Attackers might be trying to steal your information from localhost (for example, passwords, messages, or credit cards).
> NET::ERR_CERT_COMMON_NAME_INVALID
<!--more-->

{% include image.html
src="iis-express-certificate.png" caption="Figure 2: Test site using IIS Express Development Certificate." %}

If you search around, there are many methods to fix this issue. So below I only demonstrate how to use Jexus Manager to quickly resolve it.

### Step 1: Generate a new certificate to match Chrome's requirements.
The details can be found in [this article](https://www.jexusmanager.com/en/latest/tutorials/self-signed.html#self-signed-certificate-wizard), and for this specific case, you should set "localhost" as custom name, and give the certificate a friendly name ("new" for example). All other fields can use the default values.

### Step 2: Let Windows trust this certificate
So in [just a few clicks](https://www.jexusmanager.com/en/latest/tutorials/self-signed.html#to-trust-self-signed-certificate) you can get it done.

### Step 3: Change the site binding to use the new certificate
Go back to the site binding dialog and choose the new certificate instead,

{% include image.html
src="new-certificate.png" caption="Figure 3: Test site using new certificate." %}

and Chrome is now happy to accept it,

{% include image.html
src="chrome-ok.png" caption="Figure 4: Chrome accepts the new certificate." %}
