---
layout: post
title: How A Visual Studio Code Linter Works
tags: Visual-Studio-Code
permalink: /how-a-visual-studio-code-linter-works-681c8388b0ea
excerpt_separator: <!--more-->
---
I blogged about [what a basic Visual Studio Code language extension should contain](https://blog.lextudio.com/receipt-of-visual-studio-code-extension-f4fb832fd956) a few months ago, and today we focus on linter.
<!--more-->

### Linter Basic
The essential responsibility of a linter is to scan your source file and report back warnings and errors. Many programming languages do have good command line linters, so the remaining task of an extension is quite simple,

* Pass the file information to linter.
* Collect linter output and display.

### Study By Example
Now let's analyze [the ruby-linter extension](https://github.com/hoovercj/vscode-ruby-linter/blob/master/package.json#L21) to better understand how it works.

First, this extension must be triggered by a special event "onLanguage",`"activationEvents": [ "onLanguage:ruby" ]`. That means when a Ruby file is opened, the extension would be triggered.

Second, [the extension](https://github.com/hoovercj/vscode-ruby-linter/blob/master/src/extension.ts#L8) initializes a linting provider to wrap the actual command line linter. [The linting provider](https://github.com/hoovercj/vscode-ruby-linter/blob/master/src/features/rubyLinter.ts#L20) in turn calls `ruby -wc` to executing the task.

Finally, the command line output of the linter should be [parsed](https://github.com/hoovercj/vscode-ruby-linter/blob/master/src/features/rubyLinter.ts#L28).

Visual Studio Code defines the Diagnostic type to store warnings and errors, so that we can easily use regular expressions to extract information from command line output and pass it to the editor.

As the infrastructure is quite simple, you can make use of the code to implement a similar linter for another programming language in a just a few hours (I did this for reStructuredText).
