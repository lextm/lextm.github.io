---
layout: post
title: How to Abort/Cancel A Build In VSTS
tags: VSTS
permalink: /how-to-abort-cancel-a-build-in-vsts-7a41fce5a42c
excerpt_separator: <!--more-->
---
{% include image.html
src="grass-sun.jpg" caption="Copyright © Lex Li. A sunny day, Montreal." width="512px" %}

I wrote about [how to build a pipeline to host multiple Sphinx sites on Azure App Service](https://blog.lextudio.com/ci-cd-pipeline-with-vsts-and-zapier-b81d341088dd). But a remaining item is that how to abort a build when nothing (in submodules) is changed.
<!--more-->

### Changes Detection

First we need to detect changes,

1. git pull on all submodules to get new commits.
1. Use a patch file in master repo to detect changes,

``` batch
git checkout master
git add -A
git status | findstr "working tree clean"

if %errorlevel%==0 (
echo no change. abort.
powershell -executionpolicy bypass -File abort.ps1
exit /b 0
)

echo change detected. continue.
```

Most importantly, we execute a PowerShell script to abort.

### Approaches to Abort

Well, let's pause here, and go back to VSTS dashboard to review the status of different builds,

* Some of them succeeded.
* Some were cancelled in the middle by me.
* Some failed due to various errors.

So if we decide to abort a build (when nothing changes), what status should the build look like then?

Your answer to this question, in fact, decides what approach you should do next.

### Aborted as Succeeded

This can be easily achieved, by using the following PowerShell script abort.ps1 ,

``` powershell
Write-Host "##vso[task.setvariable variable=agent.jobstatus;]canceled"
Write-Host "##vso[task.complete result=Canceled;]DONE"
```

It simply tells the agent to cancel the task. Miserably, you notice that on dashboard the build is displayed as succeeded. Anyway, it works.

> Update: This approach no longer works in newer releases, and the error message is Overwriting readonly variable 'agent.jobstatus' is not permitted.

### Aborted as Failed

It is rather simple to achieve, because you simply need to modify the batch file as

``` batch
git checkout master
git add -A
git status | findstr "working tree clean"

if %errorlevel%==0 (
echo no change. abort.
exit /b 1
)

echo change detected. continue.
```

A non-zero return code is enough to fail the task and the whole build.

### Aborted as Cancelled

If you intend to see the aborted build as cancelled, things go complicated, and we need to call VSTS REST API with some special credentials.

I created [a PowerShell script on GitHub](https://github.com/lextm/vstsabort) to guide you.

Generally speaking, you need to create a special personal access token, add it to the pipeline as secret, and then pass it on to the script. The step-by-step guide can be found in the GitHub repo.

Once aborted, the build is showed as cancelled in VSTS dashboard, just like other builds you cancelled manually.

Another remaining challenge is how to properly handle private repo in pipeline, for which I wrote [another post](https://blog.lextudio.com/how-to-use-private-repos-as-submodules-in-vsts-fd69801e233f).

Stay tuned.
