---
layout: post
title: How to Acquire Dependencies for Visual Studio Code Extensions
tags: Visual-Studio-Code
permalink: /how-to-acquire-dependencies-for-visual-studio-code-extensions-9ca828cb168d
excerpt_separator: <!--more-->
---

The open source community is so large today, that you don't need to create all the wheels once again, unless really necessary.

So in my case, I had to write a reStructuredText parser on my own (as I met difficulty using existing ones), but [when I tried to build a Language Server](https://blog.lextudio.com/how-to-write-your-language-server-in-c-d9302a44f694), there are already frameworks for me to choose from.

Aha, the next step clearly is to ship the LSP component, but I hit a challenge.
<!--more-->

### Runtime Dependencies
The language server is written as .NET Core 2.0 console app, so it can be distributed as self-contained for each platforms,

* darwin_x64 for macOS
* linux_x64 for most Linux
* win32_x64 and win32_x86 for Windows

Wait! The packages are large!

{% include image.html
src="language-server-packages.png" caption="Figure 1: Language Server packages for deployment." %}

Well, should I ship all the packages in the extension to all users? It would be horrible to increase the size from only 5-MB to almost 250-MB.

> Microsoft does have .NET IL Linker to shrink the binaries, but it only cuts the total size by half roughly, which means I still need to find a better way.

{% include image.html
src="language-server-packages-2.png" caption="Figure 2: Language Server packages after IL Linker." %}

### Dear OmniSharp
There were so many times that I installed the C# extension and noticed it tried to download OmniSharp and .NET Core debugger from the internet, so no doubt I should do the same!

First, I zip all the folders separately and upload them to [a GitHub page](https://github.com/lextm/restructuredtext-antlr/releases/tag/v0.9).

> If you want to see the automated script for compilation and ZIP creation, you can refer to [dist.server.sh](https://github.com/lextm/restructuredtext-antlr/blob/master/dist.server.sh)

Second, it is time to switch to [the extension code base](https://github.com/vscode-restructuredtext/vscode-restructuredtext), and copy files from OmniSharp.

Under `src` folder you can see a few new files,

* `common.ts` A few common functions.
* `proxy.ts` Some methods to handle HTTP/HTTPS proxy.
* `platform.ts` Some methods to calculate OS dependent strings.
* `logger.ts` The logging infrastructure.
* `packages.ts` The business logic to download runtime packages.
* `ExtensionDownloader.ts` The wrapper for all above.

So everything else in the main TypeScript file (`extension.ts`) is simple,

``` typescript
function ensureRuntimeDependencies(extension: vscode.Extension<any>, logger: Logger): Promise<boolean> {
    return util.installFileExists(util.InstallFileType.Lock)
        .then(exists => {
            if (!exists) {
                const downloader = new ExtensionDownloader(_channel, logger, extension.packageJSON);
                return downloader.installRuntimeDependencies();
            } else {
                return true;
            }
        });
}
```
This function verifies if a file named `install.Lock` exists, and if not, call the downloader to download and install them.

You might wonder from where the downloader reads about the ZIP packages. Yep, that's very important a piece of information, so OmniSharp requires it to present in `package.config`,

``` json
"runtimeDependencies": [
    {
        "description": "reStructuredText Language Server for Windows (x86)",
        "url": "https://github.com/lextm/restructuredtext-antlr/releases/download/v0.9/win32_x86.zip",
        "fallbackUrl": "https://github.com/lextm/restructuredtext-antlr/releases/download/v0.9/win32_x86.zip",
        "installPath": ".rst",
        "platforms": [ "win32" ],
        "architectures": [ "x86" ],
        "installTestPath": "./.rst/Server.exe"
    },
...
```
It should explain itself, that

* `url` and `fallbackUrl` can be the links for the ZIP package.
* `installPath` indicates where the ZIP package is extracted to.
* `platforms` and `architectures` are used to mark the platform for this ZIP package.
* `installTestPath` is the final file path used for testing. Here I use the language server executable.

So once the dependency is downloaded and installed, the extension can launch the language server (here `./rst/Server.exe`) to consume its services.

For complete usage of the above code, please analyze the reStructuredText extension code base, as there are quite a few tricks you need to follow.

### What For New Package Drop?
What should happen if I need to update the ZIP packages so a new release of the Language Server could be there?

The answer is quite simple. Just bump the version of the extension, and publish it. Visual Studio Code would remove the old folder (including `./rst` folder inside) during the update process. So when the new extension is loaded, all dependencies would be downloaded again, which makes sure end users receive your new language server.

Thanks to OmniSharp guys who created the helper classes initially, so that I can save tons of hours now.
