---
layout: post
title: A Test Page for IIS CPU Throttling
tags: Visual-Studio .NET IIS
permalink: /a-test-page-for-iis-cpu-throttling-8813f76c67d
excerpt_separator: <!--more-->
---
{% include image.html
src="autumn-leaves.jpg" caption="Copyright © Lex Li. Autumn leaves, Montreal." width="512px" %}

IIS has a feature of [CPU throttling](https://docs.microsoft.com/en-us/iis/get-started/whats-new-in-iis-8/iis-80-cpu-throttling-sand-boxing-sites-and-applications) so that you can better control how to respond when a web app uses too much CPU resource.

The detailed settings are fully explored in [this page](https://docs.microsoft.com/en-us/iis/configuration/system.applicationhost/applicationpools/add/cpu). However, neither of them gives you enough hint on how to test out the feature, especially how to simulate certain level of CPU usage in your web app (unless you are a programmer yourself).
<!--more-->

Here I give you a simple ASP.NET test page (WebForms),

``` html
<%@ Page Language="C#"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" >
    <head runat="server">
        <title>Untitled Page</title>
    </head>
    <body>
        <div>
        <%var input = Request.QueryString["cpu"];
        if (input == null)
        {
            input = "100";
        }
        
        int percentage;
        if (!int.TryParse(input, out percentage) || percentage < 0 || percentage > 100)
            throw new ArgumentException("percentage");
        
        var watch = new System.Diagnostics.Stopwatch();
        watch.Start();
        while (true)
        {
            // Make the loop go on for "percentage" milliseconds then sleep the
            // remaining percentage milliseconds. So 40% utilization means work 40ms and sleep 60ms
            if (watch.ElapsedMilliseconds > percentage)
            {
                System.Threading.Thread.Sleep(100 - percentage);
                watch.Reset();
                watch.Start();
            }
        }
        %>
        </div>
    </body>
</html>

Save it as `test.aspx`, and drop it to a web app on your server.

I use the Default Web Site as example, and the following URLs can be used to generate CPU usage,

* `http://localhost/test.aspx?cpu=100`
* `http://localhost/test.aspx?cpu=90`
* `http://localhost/test.aspx?cpu=80`

Pass the percentage you want via query string, and you are done. IIS worker process `w3wp.exe` hosting this web app should use approximately the level of CPU resource as desired. Then your configuration on CPU throttling should take effect.

BTW, the trick comes from [a Stack Overflow thread](https://stackoverflow.com/questions/2514544/simulate-steady-cpu-load-and-spikes). Usually such testing needs to last for a few minutes, so make sure [ASP.NET execution timeout](https://stackoverflow.com/questions/7241046/system-web-httpexception-request-timed-out) is also increased,

``` xml
<configuration>
  <system.web>
    <httpRuntime executionTimeout="300" />
  </system.web>
</configuration>
```

> Above it sets the timeout to 300 seconds (aka 5 minutes).
