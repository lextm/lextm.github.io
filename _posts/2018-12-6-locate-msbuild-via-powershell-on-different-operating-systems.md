---
layout: post
title: Locate MSBuild via PowerShell on Different Operating Systems
tags: Visual-Studio MSBuild PowerShell Windows Linux macOS Mono
permalink: /locate-msbuild-via-powershell-on-different-operating-systems-140757bb8e18
excerpt_separator: <!--more-->
---
{% include image.html
src="crossroad-wellington.jpg" caption="Copyright © Lex Li. Crossroad on rue Wellington, Montreal." width="512px" %}

Today, PowerShell is a cross platform scripting engine, while MSBuild is also a cross platform build engine. Then how can we use PowerShell to locate MSBuild and compile the source code? You might think it is easy, but it turns out to be bit of difficult.
<!--more-->

### MSBuild on macOS/Linux/Windows
Rather interesting that MSBuild can be called directly once you install Mono, as the installation registers MSBuild in the system paths.

However, it is much more difficult to do the same on Windows, as starting from Visual Studio 2017 MSBuild is no longer installed globally but as part of Visual Studio itself. You might use the command line tool `vswhere.exe` to locate VS installation folder, and then find MSBuild path. But in PowerShell it is recommended to use the `VSSetup` module.

> Visual Studio 2019 Preview was just released by Microsoft and it changes the folder structure a little bit, so now the script looks a little lengthy.

### The Actual Script

You can run the following script on macOS/Linux as well as Windows to locate MSBuild 15.0 or above.

``` powershell
$msBuild = "msbuild"
try
{
    & $msBuild /version
    Write-Host "Likely on Linux/macOS."
}
catch
{
    Write-Host "MSBuild doesn't exist. Use VSSetup instead."
    Install-Module VSSetup -Scope CurrentUser -Force
    $instance = Get-VSSetupInstance -All -Prerelease | Select-VSSetupInstance -Require 'Microsoft.Component.MSBuild' -Latest
    $installDir = $instance.installationPath
    Write-Host "Visual Studio is found at $installDir"
    $msBuild = $installDir + '\MSBuild\Current\Bin\MSBuild.exe' # VS2019
    if (![System.IO.File]::Exists($msBuild))
    {
        $msBuild = $installDir + '\MSBuild\15.0\Bin\MSBuild.exe' # VS2017
        if (![System.IO.File]::Exists($msBuild))
        {
            Write-Host "MSBuild doesn't exist. Exit."
            exit 1
        }
    }

    Write-Host "Likely on Windows."
}

Write-Host "MSBuild found. Compile the projects."
```

> `-Prerelease` is used here because VS2019 is still a preview build. You can remove it once it reaches RTM.

You can then use the value saved in `$msBuild` to kick off project build operations.
