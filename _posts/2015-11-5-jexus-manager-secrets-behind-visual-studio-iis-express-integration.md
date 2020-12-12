---
layout: post
title: "Jexus Manager: Secrets Behind Visual Studio IIS Express Integration"
tags: Visual-Studio IIS
permalink: /jexus-manager-secrets-behind-visual-studio-iis-express-integration-834f88c8e8b
excerpt_separator: <!--more-->
---
Visual Studio has shipped IIS Express for a few releases. Behind the scene, there are some changes on the integration, but they are hidden and not documented in details. Without such knowledge, many questions were raised on StackOverflow and IIS/ASP.NET forums. In this post, I will attempt to show you some secrets and hope it helps you understand what are the common issues and their solution.
<!--more-->

> Note that we will start from Visual Studio 2015.

### Web Projects and Their IIS Express Configuration
If a new Web project called "WebApplication1" is created in VS 2015 (MVC 5 for example), the IIS Express configuration is initialized under `(solution folder)\.vs\config\applicationHost.config`.

> Note that the `.vs` folder is hidden.

Interestingly, by opening this file, we can see two sites are there,

``` xml
<site name="WebSite1" id="1" serverAutoStart="true">
  <application path="/">
    <virtualDirectory path="/" physicalPath="%IIS_SITES_HOME%\WebSite1" />
  </application>
  <bindings>
    <binding protocol="http" bindingInformation=":8080:localhost" />
  </bindings>
</site>
<site name="WebApplication1" id="2">
  <application path="/" applicationPool="Clr4IntegratedAppPool">
    <virtualDirectory path="/" physicalPath="c:\users\lextm\documents\visual studio 2015\Projects\WebApplication1\WebApplication1" />
  </application>
  <bindings>
    <binding protocol="http" bindingInformation="*:49510:localhost" />
  </bindings>
</site>
```

The first site named "WebSite1" is simply the IIS Express's default web site, while the second "WebApplication1" site is the one for your development. 

Notice that for the second site, its physical path and binding just matches the settings in your project,

``` xml
<UseIISExpress>true</UseIISExpress>
<IISExpressSSLPort />
<IISExpressAnonymousAuthentication />
<IISExpressWindowsAuthentication />
<IISExpressUseClassicPipelineMode />
<UseGlobalApplicationHostFile />
<ProjectExtensions>
    <VisualStudio>
        <FlavorProperties GUID="{349c5851–65df-11da-9384–00065b846f21}">
            <WebProjectProperties>
                <UseIIS>True</UseIIS>
                <AutoAssignPort>True</AutoAssignPort>
                <DevelopmentServerPort>49510</DevelopmentServerPort>
                <DevelopmentServerVPath>/</DevelopmentServerVPath>
                <IISUrl>http://localhost:49510/</IISUrl>
                <NTLMAuthentication>False</NTLMAuthentication>
                <UseCustomServer>False</UseCustomServer>
                <CustomServerUrl>
                </CustomServerUrl>
                <SaveServerSettingsInUserFile>False</SaveServerSettingsInUserFile>
            </WebProjectProperties>
        </FlavorProperties>
    </VisualStudio>
</ProjectExtensions>
```

Yep, if we go ahead to modify the applicationHost.config so that "WebApplication1" uses a binding such as port 49511 instead of 49510, then VS gives such an error message "Unable to launch the IIS Express web server. The start URL specified is not valid. http://localhost:49510/" when you start debugging. (Well, if for some reason this site is lost in the config file, the same error message is displayed.)

Of course, to fix it you just restore and make sure the two files (applicationHost.config and WebApplication1.csproj) are in sync.

Another way to make things out of sync is to change Web settings in VS to use another port, but this time VS is smart enough to tell you what's up by saying "The local IIS Express URL http://localhost:49512 specified for Web project WebApplication1 has not been configured. To keep these settings you need to configure the virtual directory. Would you like to create the virtual directory now?".

And by clicking Yes the sync is made for you.

> The HTTPS story is even more complicated, but if you pay enough attention you should dig out how it works.

> Please note that this post only covers localhost cases.

### Use Machine Name
Sometimes you might want to expose IIS Express externally, so that other machines in the same network can access the web server.

Then in Visual Studio 2015, you will get an error message "Unable to create the virtual directory. Cannot create the web site "http://DESKTOP-3SV65QS:49510". You must specify "localhost" for the server name." when trying to modify the project setting.

To conquer that, probably you would follow the sync tip above to modify your applicationHost.config as below,

``` xml
<site name="WebApplication1" id="2">
  <application path="/" applicationPool="Clr4IntegratedAppPool">
    <virtualDirectory path="/" physicalPath="c:\users\lextm\documents\visual studio 2015\Projects\WebApplication1\WebApplication1" />
  </application>
  <bindings>
    <binding protocol="http" bindingInformation="*:49510:DESKTOP-3SV65QS" />
  </bindings>
</site>
```

It helps, but only when you attempt to debug the web application another error message "Unable to launch the IIS Express Web server. Failed to register URL "http://DESKTOP-3SV65QS:49510" for site "WebApplication1" application "/". Error description: Access is denied. (0x80070005)" comes.

This is a typical error message for missing URL reservation at http.sys. By executing the following command as administrator, we can resolve it,

``` bash
netsh http add urlacl url=http://DESKTOP-3SV65QS:49510/ user=everyone
```

I can then debug at `http://desktop-3SV65QS:49510`. Once you open TCP port 49510 at Windows Firewall, this site can be viewed from other machines.

### HTTPS for Machine Name Case
OK, let's see how HTTPS messes things up. Assume now the project file uses this setting and the config file contains,

``` xml
<site name="WebApplication1" id="2">
  <application path="/" applicationPool="Clr4IntegratedAppPool">
    <virtualDirectory path="/" physicalPath="c:\users\lextm\documents\visual studio 2015\Projects\WebApplication1\WebApplication1" />
  </application>
  <bindings>
    <binding protocol="https" bindingInformation="*:44313:DESKTOP-3SV65QS" />
  </bindings>
</site>
```

Obviously we see this error message "Unable to launch the IIS Express Web server. Failed to register URL "https://DESKTOP-3SV65QS:44313" for site "WebApplication1" application "/". Error description: Access is denied. (0x80070005)" for missing URL reservation again, which can be easily remedied by running below as administrator,

``` bash
netsh http add urlacl url=https://DESKTOP-3SV65QS:44313/ user=everyone
```

Of course, the self-signed certificate is not trusted, but it at least works.

### Visual Studio 2013 and Below
If unfortunately you are stuck and still use old releases such as Visual Studio 2013, then your web sites live in the global IIS Express configuration file (per user), which is at `(My Documents)\IISExpress\config\applicationHost.config`. Some of the tips discussed above would need modification in order to work for those scenarios.

> To be continued with custom domain name settings.
