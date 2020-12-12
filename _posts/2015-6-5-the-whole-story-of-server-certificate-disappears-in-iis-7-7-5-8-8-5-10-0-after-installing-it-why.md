---
layout: post
title: The Whole Story of "Server Certificate Disappears in IIS 7/7.5/8/8.5/10.0 After Installing It! Why!"
tags: IIS
permalink: /the-whole-story-of-server-certificate-disappears-in-iis-7-7-5-8-8-5-10-0-after-installing-it-why-b66e802baa38
excerpt_separator: <!--more-->
---

Interestingly this issue started to be reported since the introduction of IIS 7 in Windows Vista, but I never found any good enough materials to tell the whole story. Even popular ones like [this](http://blogs.msdn.com/b/vijaysk/archive/2009/05/22/disappearing-ssl-certificates-from-iis-7-0-manager.aspx) and [this](http://nickstips.wordpress.com/2010/09/08/iis-disappearing-ssl-certificate-problem-resolved/) only cover a little bit truth for you to know why in hell such issues happen.

This post tries to show you the whole image behind the scene, so that you get both the solution and the rationale.
<!--more-->

### How to Requesting A Certificate
If you do want to buy a server certificate, you know you need to contact a CA such as VeriSign (now Symantec, and follow their [excellent article](https://support.symantec.com/en_US/article.HOWTO59214.html).

### The Problem of Disappearing Certificate
However, not all CAs give you such clear hints on how to move on step by step, and some of them even does not have IIS related guides.

For example, one CA (unnamed as I don't know its name either, learned from an online discussion) requires the following,

1. Generated a private key file using OpenSSL with `openssl genrsa -out key_name.key 2048` command.
1. Generated .csr file with `openssl req -out CSR.csr -key key_name.key -new -sha256` command.
1. Once I generated the CSR file, I provided the same CSR file to CA to get me certificate.
1. I got the certificate in .p7b format, which I converted to .cer format.

As no further guide is provided, the end user follows his/her guess to install the .cer file in IIS Manager via "Complete certificate request".

Guess what? The server certificate seems to be installed. And just after a refresh, it disappears!

### Root Cause Analysis
Unfortunately, this is just a bug of IIS Manager. Or we call it by design, as that engineer might guess you never make a request via OpenSSL (what a horrible assumption).

When IIS Manager reads this .cer file, it should scan a private location in system for a corresponding private key (which was generated and used to create the initial certificate request). As this certificate was not created following the IIS documentation, of course no private key can be found.

By all means IIS Manager should then abort and inform the user. But it stupidly continues.

Then why the certificate disappears? Well, Vijayshinva shows the reason in the first blog post I linked to, that there is another mechanism in IIS Manager who filters out certificates without private keys. It detects this disqualified certificate and hides it from you to avoid further problems.

Well, that reminds me of other tiny little things in IIS Manager that should be improved and that's why I started to develop Jexus Manager for IIS Express (and IIS of course).

### The Solution
Since the certificate process started with OpenSSL, let's end it with OpenSSL by merging the private key and the received .cer file,
``` bash
openssl pkcs12 -export -in certificate.cer -inkey privateKey.key -out certificate.pfx -certfile CACert.cer
```

The resulting PFX can be imported in IIS Manager via Import menu item in Actions panel.

### Acknowledgement
I would like to thank [Aniruddha](http://stackoverflow.com/users/4776754/aniruddha) for the great way of posting a question with enough technical details. Without this question background I could hardly imagine the cause.

[The original discussion](http://stackoverflow.com/questions/30672552/ssl-certificate-is-not-installing/30677436#30677436) was at StackOverflow.

If your CSR was made on one Windows machine and you want to import the certificate on another, then there is [a ServerFault thread](https://serverfault.com/questions/896352/iis-complete-certificate-request-when-create-certificate-request-was-from-anot).
