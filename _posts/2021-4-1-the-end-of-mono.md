---
layout: post
title: The End of Mono
tags: Mono Microsoft Linux Xamarin
excerpt_separator: <!--more-->
---

If today you still run an application on Mono, I suggest you assert again whether that decision is sustainable. A look back on the Mono history can easily tell that it can be a risky platform to use in 2021 and beyond.
<!--more-->

### 2000-2003
Though limited information is publicly available, we might assume the passion to bring C# to Linux was the primary driving force.

### 2003-2008
Novell's acquisition of Ximian seemed to add enterprise needs to the driving factors, so that things like WinForms on Mono were added and improved.

### 2008-2021
The collaboration with Unity3D (later renamed to Unity) and the shift to mobile (iOS/Android) significantly move the focus towards gaming and mobile platforms. 

It is certain that the core Mono bits (CLR and BCL) have been actively maintained and improved to empower Xamarin and Unity, compared to very limited (if not none) investment on Web stack/WinForms/GTK#.

### 2021 and Beyond
.NET 5/6 has cherry picked the most important asset (MonoVM/MonoCLR), so gaming/mobile platforms are now migrating to .NET 6 (Xamarin/Unity). Once that's done, the driving force to maintain the very large Mono distribution can shrink significantly.

As many said, .NET 6 is going to be the right platform you migrate to. Microsoft/Unity and other companies in the ecosystem have already invested a lot there.

If you don't plan to migrate to .NET 6 (no matter why), forking its repo and starting your own adventure is your freedom.

### Reference

https://corefx.lextudio.com/
