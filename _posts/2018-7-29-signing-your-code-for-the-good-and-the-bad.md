---
layout: post
title: Signing Your Code For The Good, And The Bad
tags: Visual-Studio .NET
permalink: /signing-your-code-for-the-good-and-the-bad-350b25d2b38d
excerpt_separator: <!--more-->
---
{% include image.html
src="trees-moon.jpg" caption="Copyright © Lex Li. Winter-trees and the moon, Montreal." width="512px" %}

Signing your code is a very important step to confirm that you ship something that can be trusted by others. However, it comes at a price, because you need to pay a certificate authority for a valid certificate.

> I did hesitate on buying such a certificate, as it is not cheap (consider the fact that SSL certificates for web sites are now almost free (like Let’s Encrypt). But suddenly I noticed that Microsoft MVPs are entitled free certificates from DigiCert. So I used the service to get mine.

OK. Assume somehow you get a certificate (that can sign code), then how can you use it? I believe all certificate authorities have their documentation, but always they cannot cover all the details (otherwise, I won't bother writing this post). So let the story start.
<!--more-->

### Install The Certificate on Windows

It is very important to find a secure machine and install the certificate. You should install it as private key not exportable, and only to your personal store. Then only you can use the certificate to sign code.

### What to Sign

Many files can be signed, and in my case I only choose two types,

* Executable (`.exe` and `.dll`)
* NuGet packages (`.nupkg`)

> If you need to sign other file types, please refer to the relevant documentation on those types.

### How to Verify Digital Signature

To sign the code, I use signtool of Windows SDK, and nuget command line, and both of them allow you to verify signatures.

* `signtool verify /pa /q some.dll`
* `nuget verify -All some.nupkg`

> You can read the relevant documentation to learn why such switches are chosen.

Always verify all files that you are interested in, and then we move on to actually sign the files.

### How to Sign The Files

Signing NuGet packages are easy, so I tell it first,

``` batch
nuget sign *.nupkg -CertificateSubjectName "Yang Li" -Timestamper http://timestamp.digicert.com
```

Usually you can sign executable by

``` batch
signtool sign /tr http://timestamp.digicert.com /td sha256 /fd sha256 /a *.dll
```

However, this does not cover all the possible cases.

> Please understand code signing here is completely different from strong naming your assemblies.

### Special Case for .NET SDK Projects

However, some situation can be rather complicated. That is, how can I sign the assemblies of a .NET Standard project (multi targeting) when it generates a NuGet package directly.

Obviously you need some special steps to sign the assemblies, before they are packed in the output NuGet package.

I decided to use Post Build events, which turned out to be the right way.

### Example

I listed the steps in [the batch files](https://github.com/lextm/sharpsnmplib/blob/master/dist.nuget.bat) for #SNMP Library. So the next release of #SNMP Library will be fully signed.

Stay tuned.
