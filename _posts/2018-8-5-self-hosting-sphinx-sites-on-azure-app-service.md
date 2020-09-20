---
layout: post
title: Self-Hosting Sphinx Sites on Azure App Service
tags: VSTS
permalink: /self-hosting-sphinx-sites-on-azure-app-service-a05b9db25e9a
excerpt_separator: <!--more-->
---
{% include image.html
src="trees-old-port.jpg" caption="Copyright © Lex Li. Trees near the old port, Montreal." width="512px" %}

I used to host documentation sites on Read The Docs, and [wrote about the details in previous posts](https://blog.lextudio.com/dockpanel-suite-docs-site-restructuredtext-and-visual-studio-code-d9d5a6b37a0d). But finally it came to an end, as over the weekend I moved all such sites to my own Azure App Service. So this post shows the details behind.
<!--more-->

### Self Hosting Advantages vs. Disadvantages

The advantages are clear,

* I fully own the infrastructure so anything is under control.
* No more advertisement from Read The Docs (but mine).
* Further customization can be done.

The disadvantages are also obvious,

* No more CI/CD pipeline. (Later I built mine using VSTS and Zapier.)
* No more multi-lingual support. (I don't need them right now.)
* GitHub integration is lost.
* Google Analytics integration is lost.

### Fixing The Broken Parts

Clearly I need to fix GitHub and Google Analytics integration, as they are the most critical parts.

To fix GitHub integration, I use the Gist from Mantas Vaitkunas, but of course with some modification. You can find [the version I used in DockPanel Suite Docs](https://github.com/dockpanelsuite/dockpanelsuite_docs).

Note that if your conf.py file does not locate at root (like .\something\conf.py then make sure you set edit_on_github_branch to master/something . Otherwise, the GitHub links on generated pages would miss the something part, and totally broken.

To add Google Analytics support, I followed [the steps by Srivatsan Ramanujam](https://github.com/rtfd/sphinx_rtd_theme/issues/477).

You can also check how I used it in DockPanel Suite Docs.

Then I can easily call make html to generate all pages and deploy to Azure App Service.

> Note that you should be able to also use Azure Storage Static Websites.

### URL Rewriting

Now you should notice the web site works, but old links not. That's because Read The Docs appends language specific parts such as en/latest to URLs.

I created two rules in my case,

``` xml
<rule name="removeEn" stopProcessing="true">
  <match url="^(en|zh)/latest/(.*)" />
    <action type="Redirect" redirectType="Permanent" url="http://{HTTP_HOST}/{R:2}" />
</rule>
<rule name="removeEn2" stopProcessing="true">
  <match url="^(en|zh)/latest" />
  <action type="Redirect" redirectType="Permanent" url="http://{HTTP_HOST}" />
</rule>
```

If you are familiar with IIS URL Rewriting module, easily you know what it means. I didn't redirect to HTTPS, as some sites do not work with HTTPS yet. I have another HTTPS redirection rule for HTTPS enabled sites, which I don't show here.

OK. I wrote [another post on how to set up CI/CD pipeline with VSTS](https://blog.lextudio.com/ci-cd-pipeline-with-vsts-and-zapier-b81d341088dd).

Stay tuned.
