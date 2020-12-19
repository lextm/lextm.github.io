---
layout: post
title: Integration with Snooty Language Server
tags: Visual-Studio-Code
excerpt_separator: <!--more-->
---
{% include image.html
src="marriage-place.jpg" caption="Copyright © Lex Li. A famous place to host marriage ceremonies, Montreal." width="512px" %}

[I wrote a language server prototype](https://blog.lextudio.com/a-new-restructuredtext-project-112e9ae52acb) for reStructuredText in 2017 (let's call it rstantlr), which helped create some basic functionality in the reStructuredText VSCode extension. But in the next few years I didn't have too much time to extend it further.

That approach has several limitations,

1. The language parser in C# is rather hard to improve, as the original language specification and implementation is Python based.
1. Too many gaps are there to fill.

It was a few weeks ago that I noticed MongoDB guys launched a language server for their internal documentation system named Snooty, and that language server contains reStructuredText support, which is based on docutils. And no doubt, time to integrate Snooty with reStructuredText VSCode extension.
<!--more-->

### A Quick Look At Snooty
[The MongoDB Snooty repo](https://github.com/mongodb/snooty-parser) contains the source code and a very simple guide on how to hack further.

The language server part looks quite similar to other Python based language servers (like [this](https://github.com/palantir/python-language-server/)).

The binaries are generated via GitHub Actions, and hosted as ZIP packages for macOS and Linux in [release notes](https://github.com/mongodb/snooty-parser/releases).

But you don't need to download from GitHub manually, as Snooty has [a VSCode extension](https://github.com/mongodb/snooty-vscode) of its own. You won't find this extension on VSCode Marketplace, because the extension is currently for MongoDB internal usage only.

Snooty implements several useful language server features, such as diagnostics (linting) and document links.

### Integration Plan
There are three things I think Snooty should implement so as to replace rstantlr,

* The self-contained binaries Snooty team chose are significantly big, just like rstantlr deployment packages.
* Snooty does not have a Windows package.
* Snooty does not support autocompletion like rstantlr.

In the meantime, I finally received a reply from Snooty team and learned many important pieces of information on their priorities.

Therefore, after some adjustments, I started to attack the work items above and shipped three new releases from my fork.

#### Release 1.8.2
Literally, this matches the original 0.8.2 release from MongoDB, but with two patches,

* Snooty team assume the project layout to follow their internal standards, but external documentation projects might look much different. I modified their project settings to support some typical project layouts I am familiar with.
* When a document reference appears (in `:doc:` directive), Snooty team assume `.txt` is the file extension to use, while as far as I know many people prefer `.rst`. So I patched the parser to support both.

Most important change is that I uploaded Snooty to PyPI as a new package `snooty-lextudio`, so that the VSCode reStructuredText extension can download it via `pip install snooty-lextudio`. That saves quite a lot of bandwidth as most reStructuredText users already have Python installed.

> I used 1.8.x as version number, as I wanted to distinguise my fork from the original easily. Later it turned out to be a good choice.
>
> The original Snooty 0.8.2 release was also uploaded to PyPI as `snooty` package by me, and later I uploaded `snooty-lextudio`. So when I tried to test which package was installed, it became pretty tricky. But a version check can easily tell the difference, as 0.8.2 and 1.8.2 are so different.

#### Release 1.8.3
Soon after the `snooty-lextudio` package was published, it was downloaded hundreds of times in just a couple of hours. No doubt many Windows users experience crashes as Snooty was not yet ready for Windows.

Release 1.8.3 was soon shipped to address that. The crash turned out to be quite easu to fix, because it was caused by Windows file paths, and some help from Stack Overflow showed me the right way to go.

> One small challenge here is how to force users to upgrade from 1.8.2 to 1.8.3. Luckily it was resolved by comparing the module version to 1.8.3.

#### Release 1.8.4
The final release this week successful accomplished my initial plan, by delivering the same autocompletion of file names for `:doc:` directive.

As a result, finally I could delete everything around rstantlr from the extension to make it clean again.

### The Future
All changes I made are planned to send to MongoDB team via pull requests and they can merge it after further verification. They might take over the `snooty` package from me, and ship their releases.

`snooty-lextudio` will track `snooty` but always experiment on something aggressive and experimental if possible.

Here comes the holiday season, and I really need to take some rest. So next release will definitely be in January 2021.

Stay tuned.
