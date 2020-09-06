---
layout: post
title: The Very Common Mistakes When Using IIS URL Rewrite Module
tags: IIS
permalink: /the-very-common-mistakes-when-using-iis-url-rewrite-module-a2ab7e4fee59
excerpt_separator: <!--more-->
---
{% include image.html
src="canada-maltage.jpg" caption="Copyright © Lex Li. Canada Maltage, Montreal." width="512px" %}

IIS URL Rewrite module has been a very important addition to the platform, and gives all kinds of possibilities on how you can take full control of the URLs. When proper rules are created and hosted there, many scenarios that were difficult to achieve are no longer miracles.

But flexibility comes at a price, and people can make mistakes if they blindly move on to create rules, without even playing with [the examples](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module) published by Microsoft on their excellent documentation site.

Here I show a few common mistakes that many make, and hope by reading such you can easily figure out yours.
<!--more-->

> **Tip**
>
> Do read all related articles published by Microsoft and test the sample rules on your machine to get familiar with the different parts of this module, like basic rule structure, conditions, rewrite maps, and outbound rules. Later when you start to author your own rules, such knowledge will guide you on the right track.

### Mistake 1: Wrong Value Set to `url` Attribute of `<match>` Tag

Someone can easily rush to his/her feet to write a rewrite rule as below,

``` xml
<rewrite>
  <rules>
    <rule name="Rewrite to some page">
      <match url="http://some.site/some.page" />
      <action type="Redirect" url="http://other.site/other.page" />
    </rule>
  </rules>
</rewrite>
```

It won't work at all, and the reason is that when a request on `http://some.site/some.page` arrives, IIS uses `some.page` to match the `url` attribute of the `<match>` tag. Clearly this rule needs to be modified to work with IIS's behavior.

> **Tip**
>
> Remove domain name from the url tag and add a condition to check domain name instead.

``` xml
<rewrite>
  <rules>
    <rule name="Rewrite to some page">
      <match url="some.page" />
      <conditions>
        <add input="{HTTP_HOST}" pattern="^some.site$" />
      </conditions>
      <action type="Redirect" url="http://other.site/other.page" />
    </rule>
  </rules>
</rewrite>
```

> **Tip**
>
> Also note that the URL in `<match>` tag does not have `/` at the beginning. If you put `/` there, then IIS won't be able to match the page.

### Mistake 2: Use Rewrite When Actually Redirect Is Needed

Often you want to do HTTP to HTTPS redirection and find a rule from the internet. However, instead of copying it you would like to modify it a bit,

``` xml
<rewrite>
  <rules>
    <rule name="Redirect to http" enabled="true" patternSyntax="Wildcard" stopProcessing="true">
      <match url="*" negate="false" />
      <conditions logicalGrouping="MatchAny">
        <add input="{HTTPS}" pattern="off" />
      </conditions>
      <action type="Rewrite" url="https://{HTTP_HOST}{REQUEST_URI}" redirectType="Found" />
    </rule>
  </rules>
</rewrite>
```

Why doesn't it work? Because HTTP and HTTPS use different TCP ports, and if not under proxy mode IIS won't be able to serve the content properly.

> **Tip**
>
> Do use `Redirect` as action type, and that asks the browser to connect to your HTTPS link.

### Mistake 3: Write Proxy Rules Without ARR Proxy

Microsoft decided to split most of the reverse proxy feature into another module called [Application Request Routing](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/reverse-proxy-with-url-rewrite-v2-and-application-request-routing), so many times when you design a certain type of rules you need to install ARR and enable proxy mode.

``` xml
<rewrite>
  <rules>
    <rule name="Reverse Proxy to webmail" stopProcessing="true">
      <match url="^webmail/(.*)" />
      <action type="Rewrite" url="http://localhost:8081/{R:1}" />
    </rule>
  </rules>
</rewrite>
```

If you don't enable ARR proxy mode in such cases, IIS should respond with 404 errors.

Stay tuned, as more common mistakes will be added soon.

### Sidenotes

It is very important to learn how to troubleshoot URL rewriting rules. Usually [failed request tracing](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-failed-request-tracing-to-trace-rewrite-rules) should be enabled during your experiments, so that you know why sometimes a rule fails.

You also need to use [IIS Manager](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/testing-rewrite-rule-patterns) smartly, to test out the regular expressions.
