---
layout: post
title: A WiX Installer Example, Jexus Manager
tags: Visual-Studio WiX
permalink: /a-wix-installer-example-jexus-manager-640b6586c27c
excerpt_separator: <!--more-->
---
{% include image.html
src="ikea-montreal.jpg" caption="Copyright © Lex Li. IKEA store, Montreal." width="512px" %}

Jexus Manager has been shipped as a ZIP package for long. Such a shipping method is very popular, as users can unzip the contents and run from a suitable location immediately. Tools like ILSpy also ship their binaries in this way.

However, the longer you use such an approach, the more issues you observe from logging,

* Some people launched JexusManager.exe without unzipping the whole package (Windows Explorer or other ZIP utilities can lead to such).
* Certain files can be mistakenly deleted from the unzipped folder.
* No explicit way to assert operating system and .NET Framework version.

No doubt an installer can solve such, and that's why I wrote about Inno Setup many times, which was my preferred way of packing things up. But this time I'd like to talk about WiX, an open source toolkit originated from Microsoft, and how I used it to create the simple MSI installer for Jexus Manager.
<!--more-->

### Requirements

* Windows version detection.
* .NET Framework detection.
* A Windows Start Menu shortcut.

### WiX Basics

WiX is a tool to create MSI packages. As MSI packages are in fact specialized database files, WiX uses XML as input files.

You can find the [current WiX input file](https://github.com/jexuswebserver/JexusManager/blob/master/Setup/Product.wxs) Jexus Manager uses.

The first few lines define a few variables we will use later, and their meanings are obvious.

The core XML structure is as below,

``` xml
<WiX>
  <Product>
    <Package />
    <Upgrade />
    <Feature>
      <ComponentRef Id="JexusManager" />
      ...
    </Feature>
    <Directory Id="TARGETDIR" Name="SourceDir">
      <Directory Id="$(var.PlatformProgramFilesFolder)">
        <Directory Id="INSTALLDIR" Name="$(var.ProductName)">
          <Component Id="JexusManager">
            <File />
            <File />
            ...
          </Component>
          ...
        </Directory>
      </Directory>
    </Directory>
    <Condition />
    <Condition />
    ...
  </Product>
</Wix>
```

Because Jexus Manager is pretty simple a tool, it uses the core structure like this,

* All files are in a single `<Component>` tag.
* A second `<Component>` tag is used to define the start menu item.
* A single `<Feature>` tag is used as all things will be installed as a feature.
* Multiple `<Condition>` tags are used to check Windows version and .NET Framework version.
* Other tags like `<Package>` and `<Upgrade>` are used to define metadata and handle install/upgrade scenarios.

The knowledge behind each tags can be further studied if you check out [WiX documentation](http://wixtoolset.org/documentation/).

The value of such an example is that you can always copy and paste to get started.

> Note that all GUID values must be regenerated for your own products.
>
> If you need a more complicated example (GAC, multiple features and so on), I suggest you check out [SharpDevelop](https://github.com/icsharpcode/SharpDevelop/tree/master/src/Setup).

Of course, Microsoft designed the new standard for Windows installers, called MSIX. But currently I have no plan to use it yet.
