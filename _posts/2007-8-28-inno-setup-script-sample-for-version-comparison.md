---
layout: post
title: Inno Setup Script Sample for Version Comparison
tags: Inno-Setup
permalink: /inno-setup-script-sample-for-version-comparison-d5ac8f776f1d
excerpt_separator: <!--more-->
---
An important feature that Inno Setup fails to provide is to uninstall old versions before installing a new version. When I prepared Code Beautifier Collection 6.0 Milestone 3, I started to think about this feature. Luckily because I have experienced Pascal script already, I soon found a way.
<!--more-->

Every installer place some information in your registry in order to display an item in your Control Panel's Add/Remove Programs. In your Inno Setup script under `[Setup]` section, you should specify `AppVersion` and `AppID`. For example,

``` text
AppVersion=6.0.0.1004
AppID={{F768F6BA-F164-4599-BC26-DCCFC2F76855}
```

As a result, some registry items are added.

So in `[Code]` section you can use Pascal script to do version string comparison to see if there is an older version.

``` pascal
function InitializeSetup(): Boolean;
var
  oldVersion: String;
  uninstaller: String;
  ErrorCode: Integer;
begin
  if RegKeyExists(HKEY_LOCAL_MACHINE,
    'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{F768F6BA-F164-4599-BC26-DCCFC2F76855}_is1') then
  begin
    RegQueryStringValue(HKEY_LOCAL_MACHINE,
      'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{F768F6BA-F164-4599-BC26-DCCFC2F76855}_is1',
      'DisplayVersion', oldVersion);
    if (CompareVersion(oldVersion, '6.0.0.1004') < 0) then
    begin
      if MsgBox('Version ' + oldVersion + ' of Code Beautifier Collection is already installed. Continue to use this old version?',
        mbConfirmation, MB_YESNO) = IDYES then
      begin
        Result := False;
      end
      else
      begin
          RegQueryStringValue(HKEY_LOCAL_MACHINE,
            'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{F768F6BA-F164-4599-BC26-DCCFC2F76855}_is1',
            'UninstallString', uninstaller);
          ShellExec('runas', uninstaller, '/SILENT', '', SW_HIDE, ewWaitUntilTerminated, ErrorCode);
          Result := True;
      end;
    end
    else
    begin
      MsgBox('Version ' + oldVersion + ' of Code Beautifier Collection is already installed. This installer will exit.',
        mbInformation, MB_OK);
      Result := False;
    end;
  end
  else
  begin
    Result := True;
  end;
end;
```

Do something in `InitializeSetup` gives you a chance to exit the installation if necessary.

First, I try to see if CBC was installed. Using AppID makes thing easy.
Second, I read `AppVersion` from registry named `DisplayVersion`.

Then, I compare the read result with current version, `6.0.0.1004`.

Finally, if there is a newer or the same version installed, exit. If an older version is there, uninstall it and continue to install this new version.

Because Inno Setup does not provide version string comparison function, I wrote customized code first in Delphi 2007 and then copied the code to Inno Setup script. This is an important thing, that you can debug the code in Delphi while you cannot debug it in Inno Setup,

``` pascal
function GetNumber(var temp: String): Integer;
var
  part: String;
  pos1: Integer;
begin
  if Length(temp) = 0 then
  begin
    Result := -1;
    Exit;
  end;
    pos1 := Pos('.', temp);
    if (pos1 = 0) then
    begin
      Result := StrToInt(temp);
    temp := '';
    end
    else
    begin
    part := Copy(temp, 1, pos1 - 1);
      temp := Copy(temp, pos1 + 1, Length(temp));
      Result := StrToInt(part);
    end;
end;
 
function CompareInner(var temp1, temp2: String): Integer;
var
  num1, num2: Integer;
begin
    num1 := GetNumber(temp1);
  num2 := GetNumber(temp2);
  if (num1 = -1) or (num2 = -1) then
  begin
    Result := 0;
    Exit;
  end;
      if (num1 > num2) then
      begin
        Result := 1;
      end
      else if (num1 < num2) then
      begin
        Result := -1;
      end
      else
      begin
        Result := CompareInner(temp1, temp2);
      end;
end;
 
function CompareVersion(str1, str2: String): Integer;
var
  temp1, temp2: String;
begin
    temp1 := str1;
    temp2 := str2;
    Result := CompareInner(temp1, temp2);
end;
```

These function should be placed at the beginning of `[Code]` section.
Currently, I do not know how to access `AppID` of `[Setup]` section in `[Code]` section, so all version strings are hard coded. Once I find a way to do so, those hard coded strings can be replaced.

In fact, I prefer Pascal script to MSIcode in InstallAware and I will continue using Inno Setup for CBC. Pascal programming has become part of my life.

Actually I tried to search on Google before actions, and the only useful entry returned from query is a post on AgileTrack Blog. however, that approach is a complicated approach. I believe my code is simpler and make full use of existing information in your registry. BTW, you can download the full iss file from [here](https://github.com/lextudio/codebeautifiercollection/blob/master/setup/CBC2Exe.iss)
