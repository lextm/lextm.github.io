---
layout: post
title: What Does .NET 5 Mean to You
tags: Linux Mono MonoDevelop .NET ASP.NET
permalink: /what-does-net-5-mean-to-you-c63d06b63285
excerpt_separator: <!--more-->
---
{% include image.html
src="tree-reflection.jpg" caption="Copyright © Lex Li. Tree reflection, Montreal." width="512px" %}

On Monday, [Microsoft announced .NET 5](https://devblogs.microsoft.com/dotnet/introducing-net-5/), the next major release of its popular .NET Core platform.

The version number bump (to 5) is kind of expected, as nobody wants to mess things up with .NET Framework 4.x.

Depending on how you use pieces of .NET, the announcement has different impacts.
<!--more-->

### .NET Core Users

Guys, simply keep up your good works.

### .NET Framework Users

Clearly it emphasizes again the need to port your applications from .NET Framework to .NET Core today, so that they can evolve with the new platform.

Note that it is not sure whether Visual Studio for Windows will move to .NET 5. VS has too many legacy components, so it might stick to .NET Framework.

### Mono Users

Mono has accomplished its missions, and now .NET 5 takes over the responsibilities.

> Note that Visual Studio for Mac and MonoDevelop will run on .NET 5 and above.

We should expect much better compatibility since there is no more Mono BCL. The unification of all tooling can ensure consistent behaviors everywhere.

### Xamarin and Unity Users

New Xamarin and Unity releases will switch to .NET 5 and above. This change also improves compatibility so that we won't be bothered by inconsistent behaviors here and there.

> Note that not quite sure the role of IL2CPP, and we should wait for Unity to provide an update.

### Other Thoughts

.NET Core 1 to 3 has conquered many challenges and now this platform is in its best shape. .NET 5 certainly will move this forward to a new milestone, and unify all major implementations to a common ground. Now is the best time to learn about it.
