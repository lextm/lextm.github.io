---
layout: post
title: A New reStructuredText Project
tags: Visual-Studio .NET
permalink: /a-new-restructuredtext-project-112e9ae52acb
excerpt_separator: <!--more-->
---

Microsoft/Xamarin uses VSTest to enable cross platform unit testing support for Visual Studio and Visual Studio for Mac. Therefore, I freed myself from maintaining the xUnit.net extension for the latter, and have some time for new adventures.

Visual Studio Code has been out since 2015, but become one of the most important tools around the globe. I already have [an extension for Visual Studio Code](https://github.com/vscode-restructuredtext/vscode-restructuredtext), so it is time to put more serious work on it, learn something new and build something new. But how?
<!--more-->

Well, digging into the long extension list seems to show me the obvious answer, that I should finally step up and develop a Language Server Protocol compliant component, which almost all other famous language extensions have had for long. Only such a key component can enable features that no other ways can achieve.

OK, enough background. Here comes [the new GitHub repo](https://github.com/lextm/restructuredtext-antlr).

### What does this project offer now?
* An ANTLR v4 based parser (MIT license).
* A C# based .NET Standard 1.3 library (MIT license).
* A Language Server for reStructuredText and sphinx (Apache license).
* (soon) A linter for reStructuredText and sphinx (MIT license).
* (future) A better previewer.
* (future) DocFX integration.

### Why do you choose C# and ANTLR?
* I am familiar with C#, but not Python (reStructuredText and sphinx are written in Python) or TypeScript/JavaScript.
* C# is now open source and cross platform, just like Python and TypeScript/JavaScript.
* I know a little bit ANTLR, but not other frameworks.
* Cloning existing things offers new points of view.
* I already learned a lot in the past few days on ANTLR v4 grammar design and reStructuredText syntax, which is surprisingly a great experience. More should come in the long term.

Let's see how things go in 2018. If you are interested in this project, watch it and participate :)

Merry Xmas and Happy New Year!
