---
layout: post
title: Jexus Manager Update July 2018
tags: Visual-Studio .NET IIS
permalink: /jexus-manager-update-july-2018-597ff6bc1483
excerpt_separator: <!--more-->
---
{% include image.html
src="st-john.jpg" caption="Copyright © Lex Li. The Cathedral Church of St. John the Divine, New York City" width="512px" %}

I know Jexus Manager hasn't been updated for a while. There are many reasons, and the biggest one of them is that I was working on some huge changes but I didn't know how easy/difficult it was.
<!--more-->

Even if you are quite familiar with `Microsoft.Web.Administration` API (like I once thought I were), there can be something new, as [the new test cases](https://github.com/jexuswebserver/JexusManager/commit/d47f6f283d2136bfc6d5d5ddfd47d5447c5eced4) show.

Indeed, they reveal a few interesting behaviors of MWA, on how configuration sections inherit from their parent sections. The effective override mode calculation is pretty challenging, and I completely misunderstood it in the past. Therefore, after introducing such new test cases, I had to stop development and focus on troubleshooting.

> The commit was made two days ago, but the test cases were there for months.

Initially I believed a lot need to be re-worked, but things seemed to be easier.

First, the override mode calculation only requires a better way to locate parent section. That means [the patch on it](https://github.com/jexuswebserver/JexusManager/commit/b2a82aa686f630507352cfe11b465d4c630b5684) is pretty small and can reuse existing code.

Second, I did have some ugly parent section resolution code there, but it did not cover all the proper scenarios. This missing scenario is what exactly the failed test case covers. [The patch of this](https://github.com/jexuswebserver/JexusManager/commit/f39cc00e13610e42a9c49fdcdb7c58dde0eaa3a3) is also easy, with only a few new if checks.

>I even used a few new test cases to assert internal data structures so as to confirm the inheritance relationship is correct.

I added [more test cases to cover MWA behaviors](https://github.com/jexuswebserver/JexusManager/commit/bf68c91923a5dc43462f46a70fae0482f645d852). Had I stopped at step 2, I might have missed these exciting parts.

Third, IIS configuration section definitions are stored per file (`machine.config`/root `web.config`/`applicationHost.config`/other `web.config`). It is so obvious a fact, which I neglected it earlier and made stupid code around.

Once I knew how IIS works, it was super cool to [clone that](https://github.com/jexuswebserver/JexusManager/commit/daaa1d4880ae653ae667a286be248f4a72c4d8c6) in Jexus Manager.

I had to move many methods out of `SectionGroup` to keep it as clean as possible to map IIS's version. Some were even removed completely as they were needed no more.

Four, when testing `WebSite1/test` location tag, the [parent section resolution](https://github.com/jexuswebserver/JexusManager/commit/decf5f5c279dc3a8c0705b5bddba0bb9f62cb19d) was revised again.

Five, IIS seems to treat `configProtectedData` section as special case. Otherwise, I could tell why it appears in known section definition list, while on local disk you can find it nowhere. Anyway, Jexus Manager now [knows it](https://github.com/jexuswebserver/JexusManager/commit/ac2a0d2af1fc796a282418aeabc3ddcd89d66346) always, just like IIS.

So now, even if we test [some very strange IIS behaviors](https://github.com/jexuswebserver/JexusManager/commit/ea93a9f0b91041a29813f313e20dc621cf5400c6) against Jexus Manager, we can see better compatibility.

You really should update your Jexus Manager installation to latest today.

Stay tuned.
