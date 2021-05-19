---
layout: post
title: .NET Framework Lifecycle Changes
tags: Windows .NET
excerpt_separator: <!--more-->
---

.NET Framework is still an important platform for critical applications out there, but from time to time, its support policies from Microsoft change in favor of newer platforms. If you still own applications running on .NET Framework, it is recommended that you follow such policy changes and plan your migration accordingly.
<!--more-->

### .NET Framework 4.x

In late April, Microsoft announced [an important change](https://devblogs.microsoft.com/dotnet/net-framework-4-5-2-4-6-4-6-1-will-reach-end-of-support-on-april-26-2022/) on .NET Framework support policies, and a quick summary is as below,

* Reduce your usage of .NET Framework 4.5.2/4.6/4.6.1 as they will reach end of life on April 26, 2022.

  > Yes, less than a year now if you have to migrate.

* Continue your work on .NET Framework 4.6.2 and above as they are still fully supported in coming years.

  > But keep in mind that you should migrate to .NET Core whenever possible.
  >
  > You should expect a similar announcement to move the bar upper (to a certain .NET Framework 4.7 release or 4.8).

### .NET Framework 3.5 SP1

.NET Framework 3.5 SP1 has been fully supported for a very long time now (more than 10 years), but one thing I neglected is that Microsoft changed support policies on .NET Framework 3.5 SP1 back in 2018 when Windows 10 version 1809 and Windows Server 2019 was released, so finally we expect its lifecyle to end in January 2029.

> You still need a migration plan though there are years to come.

You can read [the latest version of the support policies page](https://docs.microsoft.com/en-us/lifecycle/faq/dotnet-framework) if more details are needed.

### Takeaways

Migration is not effortless, and sometimes can be quite challenging to carry out, so whether to upgrade to a newer .NET Framework version, or .NET Core, plan early and start early.

> If you need assistance on such migration, you can reach out to us at [LeXtudio](mailto:support@lextudio.com).
