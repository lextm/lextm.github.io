---
layout: post
title: The Challenges to Enable Open With Windows Terminal
tags: Windows PowerShell "Windows Terminal"
excerpt_separator: <!--more-->
---
{% include image.html
src="canadiens-montreal.jpg" caption="Copyright © Lex Li. Tour des Canadiens Condos in Montreal." width="512px" %}

Microsoft have created several interesting open source projects in the past few years, and [Windows Terminal](https://github.com/microsoft/terminal) is surely one of the most wanted.

If you haven't played yet with it on Windows 10, you must try it out :) Gurus like Scott Hanselman already wrote tons of great articles showing various tips and tricks on how to unleash the powers of this tool.
<!--more-->
### The Missing Piece

However, ever since the initial preview, Windows Terminal does not have Windows Explorer shell integration, so you cannot easy see context menus such as "Open Windows Terminal here". There was [a long GitHub thread](https://github.com/microsoft/terminal/issues/1060) on this topic.

You can see most of the options found by the users require you to import registry keys from a file. Even if some suggested PowerShell scripts, they no longer work as expected today as Windows Terminal internals have changed. [Scott Hanselman has a GitHub repo for his INF file](https://github.com/shanselman/WindowsTerminalHere), but that repo is not quite active either.

My Solution

That's why I started my own adventure in this land yesterday and [created a brand new project](https://github.com/lextm/windowsterminal-shell).

{% include image.html
src="windows-terminal-context-menu.png" caption="Figure 1 Windows Terminal context menu items" %}

The basic idea is rather simple,

* `install.ps1` helps you install a bunch of menu items to Windows Explorer context menu.
* `uninstall.ps1` deletes everything.

However, Windows Terminal is so powerful a tool that writing such scripts can be quite challenging a task.

### Challenges

#### 1: Windows Terminal Icon

Normally for a desktop application, it installs to a fixed location and we can easily extract icons from its executable. However, Windows Terminal is a UWP app, which installs on Windows 10 differently.

For example, the main executable can be found at `$Env:LOCALAPPDATA\Microsoft\WindowsApps\wt.exe`, but it has no icon at all. That's because this executable is just a wrapper over the real executable, `C:\Program Files\WindowsApps\Microsoft.WindowsTerminal_0.11.1251.0_x64__8wekyb3d8bbwe\WindowsTerminal.exe` (for version 0.11).

As a result, `install.ps1` has to perform the described scan in order to locate the real executable and its icon. And when such attempt fails, it falls back to another option, to download the icon file from Windows Terminal GitHub repo.

#### 2: JSON Parsing

Initially I was trying to let users run `install.ps1` with PowerShell 5.1, as it ships with every Windows 10 machine. However, when the installation process started to take care of Windows Terminal settings file (in JSON format), everything broke due to a JSON parsing bug. Microsoft guys left comments in their settings file, so as to point out how users can customize the settings, but PowerShell 5.1 simply cannot parse JSON files with comments.

You can imagine there must be workarounds, but this time I rather add a warning that PowerShell 5.1 is not supported and users should install PowerShell 7 to run `install.ps1`.

#### 3: JSON Schema with Alternatives

The early release of `install.ps1` worked fine for default Windows Terminal settings. But soon a user reported [the first issue](https://github.com/lextm/windowsterminal-shell/issues/1).

Well, a surprise that Windows Terminal allows two different ways to specify profiles, and `install.ps1` must support both. Don't know how many people prefer the defaults/list approach, but it took me some time to fix that.

#### 4: Profile Icon Resolution

It would be necessary to display icons for profiles, but the flexibility of Windows Terminal increases the difficulty,

* There are special URLs for files, such as `ms-appdata:///` and `ms-appdata:///`, so they must be resolved to actual files on disk.
* There are profile icons stored in a special Windows Terminal folder, so `install.ps1` should use the profile GUID to locate its icon.

When everything fails, of course the script falls back to Windows Terminal’s icon instead.

#### 5: Icon Format Conversion

Well, Windows Terminal actually supports tons of image format (PNG, ICO, JPG and so on), but supporting everything in PowerShell is rather challenging.

So finally I decided that only ICO and PNG will be supported by `install.ps1`, and users must manually convert their icons to these two formats. The reason behind is that Windows Explorer only likes ICO files, and even converting PNG files to ICO in PowerShell (purely in PowerShell, and no third party dependencies) is rather complicated.

The conversion is carried out via a rather complicated algorithm which I learned from a Gist,

<script src="https://gist.github.com/darkfall/1656050.js?file=gistfile1.cs"></script>

and it generates good quality ICO files that meet the requirements.

#### 6: Command Elevation

The last but most difficult task is to support both normal commands and elevated commands.

Normal commands are rather simple, as they follow the pattern of `$executable -p "$name" -d "%V."`, where `$executable` is the full path of Windows Terminal executable, `$name` is the profile name, and `%V.` is the special way for a context menu item to pass its current directory to the command.

Compared to normal commands, elevated commands are rather complicated, in the pattern of `PowerShell -WindowStyle Hidden -Command "Start-Process cmd.exe -WindowStyle Hidden -Verb RunAs -ArgumentList \"/c $executable -p \"\"$name\"\" -d \"\"%V.\"\"\" "`. So here we have to first launch a hidden PowerShell instance (though it actually appears for a very short while), and from it a new cmd.exe process is launched with verb "runas". This triggers a UAC prompt, so that users can grant it administrator permissions. The elevated cmd.exe finally launches Windows Terminal as administrator.

#### 7: Command Elevation Revisit

The approach discussed above has a significant drawback, that a PowerShell window is displayed as hidden window style to elevate the command prompt process. As PowerShell is a console application, that hidden window does not hide itself well at startup, and users always see a window pops up and soon disappears (a flash). Therefore, for best user experience, an alternative approach must be found.

There was [a partially successful attempt](https://github.com/nt4f04uNd/wt-contextmenu/), but of course it lacks of many goodies. The key point here is to use WScript engine of Windows, which is surprisingly a GUI application. Thus, when we ask WScript to execute a script (VBScript) it can show no window at all.

The challenge of this approach is to write a suitable helper script to elevate Windows Terminal. After some experiments I came across this version,

``` vb
Set shell = WScript.CreateObject("Shell.Application")executable = WSCript.Arguments(0)
folder = WScript.Arguments(1)If Wscript.Arguments.Count > 2 Then
    profile = WScript.Arguments(2)
    ' 0 at the end means to run this command silently
    shell.ShellExecute "powershell", "Start-Process \""" & executable & "\"" -ArgumentList \""-p \""\""" & profile & "\""\"" -d \""\""" & folder & "\""\"" \"" ", "", "runas", 0
Else
    ' 0 at the end means to run this command silently
    shell.ShellExecute "powershell", "Start-Process \""" & executable & "\"" -ArgumentList \""-d \""\""" & folder & "\""\"" \"" ", "", "runas", 0
End If
```

### Sidenotes

Recently I have done several projects with PowerShell and it turns out to be a good language for management tasks. Hope that I can share more tips in the future.

Please send bug reports as well as pull requests.
