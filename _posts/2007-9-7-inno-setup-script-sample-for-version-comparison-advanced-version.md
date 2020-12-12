---
layout: post
title: Inno Setup Script Sample for Version Comparison (Advanced Version)
tags: Inno-Setup
permalink: /inno-setup-script-sample-for-version-comparison-advanced-version-c398d0ef18ad
excerpt_separator: <!--more-->
---
Compared to [last post on this topic](https://blog.lextudio.com/inno-setup-script-sample-for-version-comparison-d5ac8f776f1d), this time I will show how ISPP makes thing much easier.
<!--more-->

First, some important information can be defined only once and used everywhere. The definition should be written before `[Setup]` section like this,

{% raw %}
``` ini
#define MyAppID "{F768F6BA-F164-4599-BC26-DCCFC2F76855}"
#define MyAppCopyright "Copyright (C) 2005-2007 Lex Li and other contributors."
#define MyAppName "Code Beautifier Collection"
#define MyAppVersion GetFileVersion("..\bin\release\Lextm.CodeBeautifierCollection.Framework.dll")

[Setup]
AppName={#MyAppName}
AppVerName={#MyAppName}
AppPublisher=Lex Li (lextm)
AppPublisherURL=http://lextm.com
AppSupportURL=http://lextm.com
AppUpdatesURL=http://code.google.com/p/lextudio
DefaultDirName={pf}\{#MyAppName}
DefaultGroupName={#MyAppName}
AllowNoIcons=true
InfoAfterFile=After.txt
OutputDir=.
SolidCompression=true
AppCopyright={#MyAppCopyright}
VersionInfoVersion={#MyAppVersion}
VersionInfoCompany=LeXtudio
VersionInfoDescription={#MyAppName} Setup
VersionInfoTextVersion=GrapeVine
InternalCompressLevel=max
VersionInfoCopyright={#MyAppCopyright}
Compression=zip
PrivilegesRequired=admin
ShowLanguageDialog=yes
LicenseFile=License.txt
WindowVisible=false
AppVersion={#MyAppVersion}
AppID={{#MyAppID}
UninstallDisplayName={#MyAppName}
```
{% endraw %}

> Note that I have hard-coded application information here because the script is for Code Beautifier Collection only. You must specify a new GUID for your own application.

The `[Code]` section is modified too,

``` pascal
// function IsModuleLoaded to call at install time
// added also setuponly flag
function IsModuleLoaded(modulename: String ):  Boolean;
external 'IsModuleLoaded@files:psvince.dll stdcall setuponly';
 
// function IsModuleLoadedU to call at uninstall time
// added also uninstallonly flag
function IsModuleLoadedU(modulename: String ):  Boolean;
external 'IsModuleLoaded@{app}\psvince.dll stdcall uninstallonly' ;
 
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
 
function InitializeSetup(): Boolean;
var
  oldVersion: String;
  uninstaller: String;
  ErrorCode: Integer;
begin
  if IsModuleLoaded( 'bds.exe' ) then
  begin
    MsgBox( 'CodeGear RAD Studio is running, please close it and run setup again.',
             mbError, MB_OK );
    Result := False;
    Exit;
  end;
 
  if RegKeyExists(HKEY_LOCAL_MACHINE,
    'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{#MyAppID}_is1') then
  begin
    RegQueryStringValue(HKEY_LOCAL_MACHINE,
      'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{#MyAppID}_is1',
      'DisplayVersion', oldVersion);
    if (CompareVersion(oldVersion, '{#MyAppVersion}') < 0) then
    begin
      if MsgBox('Version ' + oldVersion + ' of Code Beautifier Collection is already installed. Continue to use this old version?',
        mbConfirmation, MB_YESNO) = IDYES then
      begin
        Result := False;
      end
      else
      begin
          RegQueryStringValue(HKEY_LOCAL_MACHINE,
            'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{#MyAppID}_is1',
            'UninstallString', uninstaller);
          ShellExec('runas', uninstaller, '/SILENT', '', SW_HIDE, ewWaitUntilTerminated, ErrorCode);
          if (ErrorCode <> 0) then
          begin
            MsgBox( 'Failed to uninstall Code Beautifier Collection version ' + oldVersion + '. Please restart Windows and run setup again.',
             mbError, MB_OK );
            Result := False;
          end
          else
          begin
            Result := True;
          end;
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
 
function InitializeUninstall(): Boolean;
begin
 
  // check if notepad is running
  if IsModuleLoadedU( 'bds.exe' ) then
  begin
    MsgBox( 'CodeGear RAD Studio is running, please close it and run again uninstall.',
             mbError, MB_OK );
    Result := false;
  end
  else Result := true;

  // Unload the DLL, otherwise the dll psvince is not deleted
  UnloadDLL(ExpandConstant('{app}\psvince.dll'));
 
end;
```

You can see now I make a lot of changes here in order to remove CBC pre-install helper named install.exe. Yes, using Pascal script is much easier. In fact, I find a lot of tips on [ISXKB](http://www.vincenzo.net/isxkb/index.php?title=Main_Page).

BTW, after publishing my version string comparison article on Inno Setup Newsgroup, I got several replies. According to the replies, there are already two topics on ISXKB about version string. They are [this](http://www.vincenzo.net/isxkb/index.php?title=DirectX_-_How_to_detect_DirectX_version) and [this](http://www.vincenzo.net/isxkb/index.php?title=MDAC_-_How_to_detect_version_of_MDAC_installed).

Jernej Simončič also provides his code,

``` pascal
function Count(What, Where: String): Integer;
begin
   Result := 0;
    if Length(What) = 0 then
        exit;
    while Pos(What,Where)>0 do
    begin
        Where := Copy(Where,Pos(What,Where)+Length(What),Length(Where));
        Result := Result + 1;
    end;
end;
 
 
//split text to array
procedure Explode(var ADest: TArrayOfString; aText, aSeparator: String);
var tmp: Integer;
begin
    if aSeparator='' then
        exit;
 
    SetArrayLength(ADest,Count(aSeparator,aText)+1)
 
    tmp := 0;
    repeat
        if Pos(aSeparator,aText)>0 then
        begin
 
            ADest[tmp] := Copy(aText,1,Pos(aSeparator,aText)-1);
            aText := Copy(aText,Pos(aSeparator,aText)+Length(aSeparator),Length(aText));
            tmp := tmp + 1;
 
        end else
        begin
 
             ADest[tmp] := aText;
             aText := '';
 
        end;
    until Length(aText)=0;
end;
 
 
//compares two version numbers, returns -1 if vA is newer, 0 if both are identical, 1 if vB is newer
function CompareVersion(vA,vB: String): Integer;
var tmp: TArrayOfString;
    verA,verB: Array of Integer;
    i,len: Integer;
begin
 
    StringChange(vA,'-','.');
    StringChange(vB,'-','.');
 
    Explode(tmp,vA,'.');
    SetArrayLength(verA,GetArrayLength(tmp));
    for i := 0 to GetArrayLength(tmp) - 1 do
        verA[i] := StrToIntDef(tmp[i],0);
        
    Explode(tmp,vB,'.');
    SetArrayLength(verB,GetArrayLength(tmp));
    for i := 0 to GetArrayLength(tmp) - 1 do
        verB[i] := StrToIntDef(tmp[i],0);
 
    len := GetArrayLength(verA);
    if GetArrayLength(verB) < len then
        len := GetArrayLength(verB);
 
    for i := 0 to len - 1 do
        if verA[i] < verB[i] then
        begin
            Result := 1;
            exit;
        end else
        if verA[i] > verB[i] then
        begin
            Result := -1;
            exit
        end;
 
    if GetArrayLength(verA) < GetArrayLength(verB) then
    begin
        Result := 1;
        exit;
    end else
    if GetArrayLength(verA) > GetArrayLength(verB) then
    begin
        Result := -1;
        exit;
    end;
 
    Result := 0; 
end;
```

Thanks Jernej.
