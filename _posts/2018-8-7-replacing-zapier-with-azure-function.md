---
layout: post
title: Replacing Zapier With Azure Function
tags: .NET Azure VSTS GitHub
permalink: /replacing-zapier-with-azure-function-23e8c66bd8bc
excerpt_separator: <!--more-->
---
{% include image.html
src="canal-bridge.jpg" caption="Copyright © Lex Li. A bridge over the canal, Montreal." width="512px" %}

I just blogged about that I used [Zapier to trigger VSTS build](https://blog.lextudio.com/ci-cd-pipeline-with-vsts-and-zapier-b81d341088dd), which works well. And I love it, except

* It seems to ask for too many permissions (both on VSTS/GitHub sides).
* Such permissions are granted for a very long time (not a one time setup).
* Its free plan of course has limitation.
<!--more-->

I still remembered that [when I attempted to abort VSTS build](https://blog.lextudio.com/how-to-abort-cancel-a-build-in-vsts-7a41fce5a42c), I read about how to call VSTS REST API in C#.

> In fact, the first revision of that abort tool was in C#.

With [the same snippet](https://docs.microsoft.com/en-us/rest/api/vsts/build/?view=vsts-rest-4.1), I can easily trigger a build from code by sending a suitable POST request.

All I need is just the URL path and a one-time Personal Access Token.

So what if I write a tool myself to trigger the build? Where should I host it? In what form?

Luckily I was evaluating Azure Function recently, and I did know I can easily paste just a small code snippet and run it.

The only missing part was to let GitHub push talk to this Azure Function, and well Google took me directly to [the spot](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-github-webhook-triggered-function#create-an-azure-function-app).

Easy? Time to put them all together.

1. Follow Microsoft's sample to create the Azure Function on Azure. Once finished, you have a URL and a secret.
1. Go to the GitHub repos you want to track, and add a web hook in them with the info you got in step 1.
1. Modify the Azure Function, and use the following code snippet to trigger a build.

``` csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();
    // Extract github comment from request body
    string gitHubComment = data?.comment?.body;
    var pat = "xxxxxxxxx";
    // trigger a new build.
    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Accept.Clear();
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic",
            Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("{0}:{1}", "", pat))));
        var url = $"https://xxxx.visualstudio.com/xxxx/_apis/build/builds?api-version=4.1";
        Console.WriteLine(url);
        using (HttpResponseMessage response = await client.PostAsync(
            url,
            new StringContent(
            "{ \"definition\": {\"id\": 2}, \"reason\": \"manual\" }",
            Encoding.UTF8,
            "application/json")))
        {
            response.EnsureSuccessStatusCode();
            string responseBody = await response.Content.ReadAsStringAsync();
            Console.WriteLine(responseBody);
        }
    }
    
    return req.CreateResponse(HttpStatusCode.OK, "From Github:" + gitHubComment);
}
```

It is so obvious what the code does, so I won't explain further.

> Note that the personal access token has a life span (one year max), so I do need to replace it later with another one.

Then I went on to remove all my Zaps from Zapier and also disconnected all linked accounts. I might use Zapier next time for something else, but right now Azure Function serves me reliably.

Stay tuned.
