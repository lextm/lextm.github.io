---
layout: post
title: How to Write Your Language Server in C#
tags: Visual-Studio .NET
permalink: /how-to-write-your-language-server-in-c-d9302a44f694
excerpt_separator: <!--more-->
---

I just wrote about [my plan](https://blog.lextudio.com/a-new-restructuredtext-project-112e9ae52acb) to enhance reStructuredText extension for Visual Studio Code by adding a language server.

While it is going to be a long road ahead, I already got some initial results. For this post, I would like to share some tips on the Language Server Protocol, so that if you are eager to start your own today as a C# developer, you can do the same.
<!--more-->

### Language Server Protocol
There would be no better place than [this Microsoft site](https://microsoft.github.io/language-server-protocol/). So not only Visual Studio Code supports it, other major editors and IDE vendors are starting to adopt it,

* Eclipse (including Che)
* emacs
* Atom
* Visual Studio

So simply speaking, your Visual Studio Code extension should initialize a client of LSP (which is as simple as just copying some code snippets), and then hooking an external program as LSP server, everything is going to work!

Thanks to Visual Studio Code and Erick Gamma for making this a reality.

### LSP .NET Implementations
Next, how to choose a framework that implements the protocol? Well, I collected a bunch projects, so let's go over them one by one.

* Josh Johnson created vscode-dotnet in 2016 and published it in May. Sadly this project didn't last long enough.
* OmniSharp team started their pure C# implementation just a few months ago in March 2017. Not bad, but their only sample is OmniSharp itself, which is a monster... Thus, I didn't choose it.
* CXuesong created his own C# framework in May 2017, two months behind OmniSharp, but he alone achieved almost the same results, and he finished a Language Server prototype for Wikitext. Most importantly, his framework comes with a simplest sample project, which helps me get started in a just a few hours!
* Adam Friedman created another implementation in September 2017 and used it to help Visual Studio.
* Microsoft released its own Visual Studio LSP Preview, but not sure if they are going to open source the implementation behind. If they do, they will compete in this field with others. If they happen to use any of above, then that framework would receive some more love :)

### Start My Own
Roughly below are the steps I used,

1. Clone CXuesong's repo to local, and follow the instructions to get the demo extension work. Of course, I met some tiny issues, but they are easy to fix, and the pull request was merged.
1. (Important!) Modify my own extension to load the demo language server. Only via this you can safely confirm that you have the necessary LSP client ready on Visual Studio Code.
1. Start to hack the demo Language Server to implement reStructuredText logic, and make it a useful server.

Don't jump too soon to step 3, as LSP is complex. Make sure that step 2 works would give you a solid foundation to move on.

I still have more to dig in the coming weeks, but it is so interesting to see the parser gives so many possibilities.

### Start Your Own
What language do you use often in Visual Studio Code but currently lacks of a good extension with language server? Help them out and don't wait!
