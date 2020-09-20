---
layout: post
title: VSCode reStructuredText Extension, Synchronized Preview Support
tags: Visual-Studio-Code reStructuredText
permalink: /vscode-restructuredtext-extension-synchronized-preview-support-3f54ebdd3416
excerpt_separator: <!--more-->
---
{% include image.html
src="doves-sunset.jpg" caption="Copyright © Lex Li. Doves in the sunset, Montreal." width="512px" %}

I just shipped the 81.0.0 release of this extension. As it is worth a blog post, you can see how big the changes are there.
<!--more-->

### Summary on Improvements

First, a Microsoft guy approached me and informed kindly that due to recent changes in VSCode, I should try to switch to [Webview API](https://code.visualstudio.com/docs/extensions/webview) instead.

> Appreciate it :) Microsoft.

So 81.0.0 ships with the required changes.

Second, [synchronized preview](https://github.com/vscode-restructuredtext/vscode-restructuredtext/issues/38) has been there for Markdown files for a while. I opened this issue in February 2017, and finally could close it.

Third, the status bar element (which indicates the active preview engine) does not show at the desired time, and [that has been fixed](https://github.com/vscode-restructuredtext/vscode-restructuredtext/issues/98).

### Stories Behind The Scene
This release is bigger than it seems to be. In fact, it demonstrated another time that open source collaboration is great.

OK, let's turn back time to February 2017, and Microsoft released [VSCode 1.9](https://code.visualstudio.com/updates/v1_9#_markdown-preview-and-editor-integration) with synchronized Markdown preview support.

I did check their code base but it was a lot to change if I were about to implement it on my own. Clearly I did have other priorities to take care of.

I subscribed to many reStructuredText related repos on GitHub, so no wonder I knew their progress and found that RST-vscode [started to port sync scrolling](https://github.com/tht13/RST-vscode/commits/master) in September.

I waited till all other high priority items had been done, and then started to port what Thomas did.

It gave me a very nice chance to clean up the current code base (due to the changes we made to add status element and adapt to the workspace API,

* Removed all preview related code.
* Inserted Thomas's code.
* Added the status element first, so that I could let it appear whenever needed.
* Added `conf.py` iteration code, so that active preview engine could be selected properly.
* Added if branch to enable Sphinx based preview.
* Fixed links as Webview has its own schema.
* Learned how line numbers are injected for docutils, and revised Sphinx related code to do the same.

81.0.0 ships with all such, while the upcoming 82.0.0 implements more,

* Support preview page revival when VSCode is restarted.
* Fixed all test cases.

### Secrets on Synchronized Preview

If you really want to understand the technical details, here it is.

Both docutils and Sphinx generates HTML from reStructuredText. There are no clear mappings to decide which reStructuredText elements are associated with their corresponding HTML elements, but we can assume there might be a one-to-one mapping by counting rows.

Thomas uses [the algorithm](https://github.com/vscode-restructuredtext/vscode-restructuredtext/blob/81.0.0/src/features/previewContentProvider.ts#L110) to inject source code line numbers in HTML elements, which works fine for docutils generated preview pages. However, Sphinx generates much more elements in its preview pages (for theming), so I have to invent [a hacky approach](https://github.com/vscode-restructuredtext/vscode-restructuredtext/blob/81.0.0/src/features/previewContentProvider.ts#L75).

It is so hacky at this moment, so it might lead to mistakes on certain files. I hope to find a better way in future releases.

Once the line numbers are injected into the HTML pages, it is the script files (like `pre.js` and `index.js`) that enable the interaction between Webview and the code editor. This part is also what Markdown extension uses.

If you are working on a similar extension and would like to port synchronized preview feature, hope the explanation can save you a few hours :)
