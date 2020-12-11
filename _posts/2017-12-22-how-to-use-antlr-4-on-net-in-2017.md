---
layout: post
title: How to Use ANTLR 4 on .NET in 2017
tags: Visual-Studio .NET
permalink: /how-to-use-antlr-4-on-net-in-2017-d8bfe7efa74c
excerpt_separator: <!--more-->
---
{% include image.html
src="oct-pujiang.jpg" caption="Copyright © Lex Li. OCT, Pujiang Town, Shanghai." width="512px" %}

I once blogged heavily about how to use ANTLR on .NET, and you can find [all links here](https://blog.lextudio.com/antlr-article-series-6f0f38a0a6da).

And [my last blog post on ANTLR](https://blog.lextudio.com/how-to-use-antlr-4-on-net-4361915b670f) was in 2014. You can still learn something useful from the old post.

But today it is time to provide some more information after three years.
<!--more-->

### .NET Standard Support

ANTLR v4 has evolved a lot. It is now .NET Standard 1.3 compliant, and also has some experimental IKVM support (so that you might avoid installing Java runtime to compile the grammar). However, IKVM is no more maintained, so it is still recommended that you stick to Java runtime.

In the meantime, if you use ANTLR v4 in the latest MSBuild 15 projects, [the project file](https://github.com/lextm/restructuredtext-antlr/blob/master/ReStructuredText/ReStructuredText.csproj) can be very clean.

### Listener and Visitor

Last post I could not show an example on how to properly utilize ANTLR v4's new generated listener and visitor. This time finally I have [an open source project](https://github.com/lextm/restructuredtext-antlr/blob/master/ReStructuredText/restructuredtextParser.cs) to demonstrate the technique.

You can see that by adapting to the new mechanism, the grammar file indeed has no action (C# code), and all the extra processing logic is in the visitor hierarchy. It would be possible to reuse the grammar file for other projects (to develop a Java parser for the grammar if you like).

### JetBrains Rider

Now I prefer to develop the grammar in Rider, which has great ANTLR support.

{% include image.html
src="antlr-rider.png" caption="Figure 1: ANTLR support in Rider." %}

First, when you try to edit .g4 file, Rider prompts you to install the extension. Once installed, you get the ANTLR Preview and Tool Output panels.

To quickly test a rule, simply keep the .g4 file tab at top, and then right click on the rule you would like to test ("parse" rule for example), and then in the left box of ANTLR Preview panel, a piece of text can be input (or open a text file). The right box would show almost immediately how the grammar would parse the text into AST.

If any exception happens, it is reported under the left box and also highlighted around the corresponding text via tooltip, so you can easily figure out what should be fixed.

As this grammar has no more action, there is no need to even change "language" option to Java to have the previewer working. What is a great piece of work!

Now you can run C# unit test cases and ANTLR Preview in the same IDE, so developing a grammar is easier than ever.

### Some More Tips on v3/v4 Migration Coming

In last post I did share some tips, but without examples. I might find some time in the next few weeks to dig real world examples (for this reStructuredText grammar of course), as I did come across far too many issues in the past few days, and successfully figured out a few ways to address them (though ugly).

Stay tuned.
