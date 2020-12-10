---
layout: post
title: IntelliSense for reStructuredText Extension, Initial Offering
tags: Visual-Studio-Code
permalink: /intellisense-for-restructuredtext-extension-initial-offering-6886b17d874b
excerpt_separator: <!--more-->
---
IntelliSense (or autocomplete in general) is one of the key features we prefer to use a tool to write code (instead of raw editors like Notepad).
<!--more-->

### Code Snippets
This extension already provides some basic autocompletion functionality, which is the code snippets,

{% include image.html
src="code-snippets.png" caption="Figure 1: Code snippets like 'note'." %}

Once triggered when you input a keyword (even partially), Visual Studio Code would expand the template associated.

But code snippets can only implement certain kinds of logic. So they are not smart enough.

### Smart File Path Insertion
With the reStructuredText parser and Language Server, now I can start to add context-aware IntelliSense features in the extension, and the first offering is something I personally call "Smart File Path Insertion".

Take a look at the screen shot above, you will see that to link to other articles in the same sphinx project, I need to use `:doc:` (cross-referencing documents).

It was painful to fill in the file path manually, as

* I need to move my eyes to the EXPLORER panel
* Maybe expand a few nodes in the folder structure to navigate which document I should link
* Type the file path (and remember not to add the file extension).

Starting from release 47.0.0, you can receive some assistance from this extension.

First, enable IntelliSense following [the steps](https://github.com/vscode-restructuredtext/vscode-restructuredtext/blob/master/docs/sphinx.md#intellisense-settings).

Second, restart Visual Studio Code.

Third, open the folder once again, and see in OUTPUT panel the following,

``` txt
Installing reStructuredText dependencies…
Platform: darwin, x86_64
Downloading package 'reStructuredText Language Server for macOS (x64)' (12454 KB) ……………….. Done!
Installing package 'reStructuredText Language Server for macOS (x64)'
Finished
```

Now when you try to create a link and press the "/" key, see what pops up,

{% include image.html
src="intellisense.png" caption="Figure 2: Basic Intellisense." %}

Well, do you like it? It should save you several seconds per link at least, and several minutes per day I believe.

More to come in the next few weeks :)
