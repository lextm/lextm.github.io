---
layout: post
title: What Should You Check When Visual Studio Cannot Debug ASP.NET Core Projects
tags: Visual-Studio .NET IIS
permalink: /what-should-you-check-when-visual-studio-cannot-debug-asp-net-core-projects-4b5db8c5e129
excerpt_separator: <!--more-->
---
{% include image.html
src="st-lawrence-river.jpg" caption="Copyright © Lex Li. St Lawrence River in Quebec City." width="512px" %}

Debugging is very important a process for developers to learn more about the development environment and the code base. So frustration can be a lot if suddenly your development tool fails to debug.

I [documented a few typical Visual Studio and IIS Express related issues](https://blog.lextudio.com/jexus-manager-secrets-behind-visual-studio-iis-express-integration-834f88c8e8b) that can prevent debugging from working. But you already noticed that they were about ASP.NET 4.x projects, and do not apply to ASP.NET Core projects.

I [wrote about ASP.NET Core and Visual Studio once](https://blog.lextudio.com/how-visual-studio-launches-iis-express-to-debug-asp-net-core-apps-d7fd3677e3c3). However, I didn't reveal enough on all the issues you might hit. So this post would serve the missing pieces.
<!--more-->

### Unable to connect to web server IIS Express

OK, let's get started from an error message,

{% include image.html
src="unable-to-connect.png" caption="Figure 1: Unable to connect error dialog." %}

Horrible, isn't it? How should we find out the cause of such an error when the message provides so limited information?

Wait a minute, and it does say 'IIS Express', a very special term that might be useful later. Correct, if we search for the string "IIS Express" (don't forget the space in between) in the whole solution, we can easily locate a few spots,

``` text
    Find all "IIS Express", Subfolders, Find Results 1, Entire Solution, ""

    C:\Users\lextm\source\repos\mvccoretest\mvccoretest\Properties\launchSettings.json(11): "IIS Express": {

    C:\Users\lextm\source\repos\mvccoretest\.vs\config\applicationhost.config(12): within this file and are understood by the IIS Express.

    C:\Users\lextm\source\repos\mvccoretest\.vs\config\applicationhost.config(14): %IIS_USER_HOME% — The IIS Express home directory for the user

    C:\Users\lextm\source\repos\mvccoretest\.vs\config\applicationhost.config(16): %IIS_BIN% — The location of the IIS Express binaries

    Matching lines: 4 Matching files: 2 Total files searched: 54
```

Of course, `applicationHost.config` is not yet useful here, as we know the lines come from the comments. `launchSettings.json` is the only thing we should move on to.

### The Magic of `launchSettings.json`

When I wrote about `VSIISExeLauncher.exe`, I had no idea how `launchSettings.json` works. [It turns out to be very important a piece of magic](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments).

So simply speaking, the `IIS Express` section we found in this file defines a debugging profile, which allows Visual Studio to debug the project using IIS Express as the host.

Now back to the error message. When it says it was "Unable to connect to web server 'IIS Express'", we know the IIS Express settings must be broken. So what are the expected settings?

Read `launchSettings.json` once again, and we can see

``` json
{
    "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iisExpress": {
            "applicationUrl": "http://localhost:49875/",
            "sslPort": 0
        }
    },
    "profiles": {
        "IIS Express": {
            "commandName": "IISExpress",
            "launchBrowser": true,
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        },
        "mvccoretest": {
            "commandName": "Project",
            "launchBrowser": true,
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            },
            "applicationUrl": "http://localhost:49876/"
        }
    }
}
```

Right. It does have a section called "iisSettings", and that's exactly where the desired settings are,

* applicationUrl: "http://localhost:49875/"
* sslPort: 0

The zero value of `sslPort` means Visual Studio expects to launch an HTTP site, and the URL should be `http://localhost:49875`.

Time to jump to `applicationhost.config` and great, we spot the cause in just a minute,

``` xml
<site name="mvccoretest" id="2">
    <application path="/" applicationPool="Clr4IntegratedAppPool">
        <virtualDirectory path="/" physicalPath="C:\Users\lextm\source\repos\mvccoretest\mvccoretest" />
    </application>
    <bindings>
        <binding protocol="http" bindingInformation="*:4987:localhost" />
    </bindings>
</site>
```

Got it? The site mapping to this project in fact has no binding that would accept the URL `http://localhost:49875/`. By adding a suitable binding, we can resolve the error message.

> Note that Visual Studio 2017 15.7 (Preview 2.0) is smart enough to fix broken `applicationhost.config` every time I change `launchSettings.json`. So Microsoft finally decides to take actions on the right track.

### HTTPS Scenario

OK. Now let's change `launchSettings.json` to work on HTTPS,

``` json
{
    "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iisExpress": {
            "applicationUrl": "http://localhost:49875/",
            "sslPort": 44322
        }
    },
    "profiles": {
        "IIS Express": {
            "commandName": "IISExpress",
            "launchBrowser": true,
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        },
        "mvccoretest": {
            "commandName": "Project",
            "launchBrowser": true,
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            },
            "applicationUrl": "http://localhost:49876/"
        }
    }
}
```

I change `sslPort` to 44322, so the site should be accessed via `https://localhost:44322/`.

Then I intentionally break `applicationhost.config` by changing it to,

``` xml
<site name="mvccoretest" id="2">
    <application path="/" applicationPool="Clr4IntegratedAppPool">
        <virtualDirectory path="/" physicalPath="C:\Users\lextm\source\repos\mvccoretest\mvccoretest" />
    </application>
    <bindings>
        <binding protocol="http" bindingInformation="*:49875:localhost" />
        <binding protocol="https" bindingInformation="*:44321:localhost" />
    </bindings>
</site>
```

Visual Studio does not even bother to report an error this time. Sigh. So any developer hitting the issue would only see a browser dependent message (Firefox in my case),

{% include image.html
src="firefox-error.png" caption="Figure 2: Unable to connect error in Firefox." %}

It's a bad idea and I think I should now report to Microsoft.

Another interesting fact is that `sslPort` does have a default value of `0`, so it can be deleted from `launchSettings.json` if you like. However, if `applicationUrl` is deleted, an error message would be showed ("The IIS Express settings are missing the App Url property. This is required to configure IIS express to run the site."),

{% include image.html
src="app-url-error.png" caption="Figure 3: App Url error dialog." %}

Note that how horrible is the error message itself, with typo and not enough information for readers to know what exactly is missing.

### Sidenote

I might add more contents in the future, as now I am enhancing Jexus Manager Visual Studio Project Diagnostics tool to [support ASP.NET Core projects](https://www.jexusmanager.com/en/latest/tutorials/vs-diagnostics.html).

Stay tuned.
