# Group policy cookbook

## Disable Chrome/Edge password manager

In your user configuration GPO:

* User Configuration > Policies > Administrative Templates > Google Chrome > Set "Password manager" to "Disable"

* User Configuration > Policies > Administrative Templates > Microsoft Edge > Set "Enable saving passwords to the password manager" to "Disable"

## Disable Internet Explorer

In your computer configuration GPO:

1. Computer Configuration > Administrative Templates > Windows Components > Internet Explorer > Set "Disable Internet Explorer 11 as a standalone browser" to "Enabled"

2. Under Options, Set "Always" (to notify users every time they're redirected from IE11 as this will redirect them to Edge)

## Disable Run

In your user configuration GPO:

User Configuration > Administrative Templates > Start Menu and Taskbar > Set "Remove Run menu from start menu" to "Enabled"

## Delete contents of Public Desktop on each boot

In your computer configuration:

Computer Configuration > Preferences > Windows Settings > Files > New File 

Configure parameters as follows:
* Action: Delete
* Delete file(s): %PUBLIC%\\Desktop\\*


## Disable Wordpad / Notepad

In your computer configuration GPO:

Go to Computer Configuration > Policies > Local Policies / Security Options > Software Restriction Policies

Under Additional Rules, create New Path rule and configure as follows:

**Notepad**

* Path: C:\\Windows\\System32\\notepad.exe
* Security Level: Disallowed 

**Wordpad**

* Path: %PROGRAMFILES%\\Windows NT\\Accessories\\wordpad.exe
* Security Level: Disallowed 


## Disable Paint3D

In your computer configuration GPO:

Go to Computer Configuration > Policies > Local Policies/Security Options > Software Restriction Policies

Under Additional Rules, create New Path Rule and configure as follows:

* Path: %PROGRAMFILES%\\WindowsApps\\Microsoft.MSPaint* 
* Security Level: Disallowed 

## Disable MS News feed in taskbar

In your computer configuration GPO:

Computer Configuration > Policies > Administrative Templates > Windows Components > News and interests > Set "Enable news and interests on the taskbar" to "Disabled"

**References**

1. [Article explaining the procedure](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/group-configuration-news-and-interests-on-the-windows-taskbar/ba-p/2281005)

2. [GP Administrative templates if missing](https://www.microsoft.com/en-us/download/details.aspx?id=103060)


## Deactivate MS Search in taskbar

In your user configuration GPO:

Create registry key:

Hive: HKEY_CURRENT_USER
Key path: SOFTWARE\Microsoft\Windows\CurrentVersion\Search 
Value name: SearchboxTaskbarMode
Value type: REG_BINARY 
Value data: 0

**References**

1. [Useful Microsoft forum post](https://learn.microsoft.com/en-us/answers/questions/780866/windows-10-remove-cortana-search-box-from-task-bar)


## Disable Cortana

In your computer configuration GPO:

Computer Configuration > Administrative Templates > Windows Components > Search > Set "Allow Cortana" feature to "Disabled".

**References**

1. [Microsoft article about configuring Cortana](https://learn.microsoft.com/en-us/windows/configuration/cortana-at-work/cortana-at-work-policy-settings)


## Disable executing Speech Recognition app

In your computer configuration GPO:

Computer Configuration > Policies > Local Policies/Security Options > Software Restriction Policies

Under Additional Rules, create New Path Rule and configure as follows:

* Path: %WINDIR%\\Speech\\Common\\sapisvr.exe
* Security Level: Disallowed 


## Disable Microsoft Solitaire

In your computer configuration GPO:

Computer Configuration > Policies > Local Policies/Security Options > Software Restriction Policies

Under Additional Rules, create New Path Rule and configure as follows:

* Path: %PROGRAMFILES%\\WindowsApps\\Microsoft.MicrosoftSolitaire*
* Security Level: Disallowed 


## Disable MS Narrator

In your user configuration GPO:

User Configuration >  Administrative Templates > System

* Under "Don't run specified Windows applicaitons" add "narrator.exe" to the list

Result: "Operation has been cancelled rue to restrictions in effect on this computer. Contact system administrator"


## Disable offline files

In your computer configuration GPO:

Computer Configuration > Administrative Templates > Network > Offline Files > Set "Allow or Disallow use of the Offline Files" feature to "Disabled"


## Disable IPv6

In your computer configuration GPO:

Create new registry key:

Registry Hive: HKEY_LOCAL_MACHINE
Registry Path: SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters
Value Name: DisabledComponents
Value Type:	REG_DWORD
Value: 255

**References**

1. [Microsoft article about IPV6 configuration](https://learn.microsoft.com/en-US/troubleshoot/windows-server/networking/configure-ipv6-in-windows)
   
2. [ADMX article about GP options regarding IPV6](https://admx.help/?Category=IPv6GroupPolicy&Policy=Microsoft.Policies.ConfigureIPv6::ConfigureIPv6_policy)



## Disable Xbox and Games

Three steps for this.

### 1. Disable Recording and Broadcasting

In your computer configuration GPO:

Computer Configuration > Administrative Templates > Windows Components > Windows Game Recordings and Broadcasting 

* Set "Enables or disables Windows Game Recording and Broadcasting" to "Disabled"

Expected Registry changes:
* HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR AppCaptureEnabled to 0
* HKEY_CURRENT_USER\System\GameConfigStore\GameDVR_Enabled to 0

Expected results:
* Recording is disabled both active and backround recording
* Shortcuts for accessing app features will stop working

### 2. Disable executing the Game bar app and xbox services

In your computer configuration GPO:

Go to Computer Configuration > Policies> Local Policies/Security Options > Software Restriction Policies

Under Additional Rules, create New Path Rule and configure as follows:

* Path: %PROGRAMFILES%\WindowsApps\Microsoft.Xbox*
* Security Level: Disallowed 
* Description: Microsoft Xbox Game related services will be blocked

### 3. Disable Game Mode

In your user configuration GPO:

Create registry key:

* Hive: HKEY_CURRENT_USER 
* Key path: Software\Microsoft\GameBar
* Value name: AutoGameModeEnabled
* Value type: REG_DWORD 
* Value data: 0


## Disable Edge browser

In your user configuration GPO:

User Configuration >  Administrative Templates > System

Under "Don't run specified Windows applicaitons" add "msedge.exe" to the list

**Note:** Make sure to configure other browser as default browser to prevent poor user experience.


## Disable Web Results in Windows Search

### Via GPO options

In your user configuration GPO:

User Configuration > Administrative Templates > Windows Components > File Explorer 

* Set "Turn off display of recent search entries in the File Explorer search box" to "Enabled"

### Via registry modification

In case there is no option for this there (it might be exclusive to Enterprise edition), add registry key as follows.

In your computer configuration GPO:

Add registry key:

* Hive: HKEY_LOCAL_MACHINE 
* Key path: SOFTWARE\Policies\Microsoft\Windows\Explorer 
* Value name: DisableSearchBoxSuggestions
* Value type: REG_BINARY 
* Value data: 1


## Disable Windows Hotkeys

This will remove some usefull shortcuts such as win + E, win + shift + s (which triggers snip and sketch overlay), etc.

### Via GP options

In your user configuration GPO:

User Configuration > Administrative Templates > Windows Components > File Explorer

* Set "Turn off Windows Key hotkeys" to "Enabled".

### Via registry modification

In your user configuration GPO:

Create registry key:

* Hive: HKEY_CURRENT_USER
* Key path: SOFTWARE\Microsoft\Windows\CurrentVersion\Policies
* Value name: NoWinKeys
* Value type: REG_BINARY 
* Value data: 1

## References

1. [ADMX article about GP options](https://admx.help/?Category=Windows_10_2016&Policy=Microsoft.Policies.WindowsExplorer::NoWindowsHotKeys)


## Make Chrome default browser / Set deault applications by file type

1. First create application association (AppAssociation.xml) file as below

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DefaultAssociations>
	<Association Identifier=".pdf" ProgId="AcroExch.Document.DC" ApplicationName="Adobe Acrobat Reader DC" />
	<Association Identifier=".htm" ProgId="ChromeHTML" ApplicationName="Google Chrome" />
	<Association Identifier=".html" ProgId="ChromeHTML" ApplicationName="Google Chrome" />
	<Association Identifier="http" ProgId="ChromeHTML" ApplicationName="Google Chrome" />
	<Association Identifier="https" ProgId="ChromeHTML" ApplicationName="Google Chrome" />
</DefaultAssociations>
```

2. Save the file somewhere accessible by all domain computers
   
3. In your computer configuration GPO:

Computer configuration > Policies > Administrative templates > Windows Components/File Explorer
  
* Set "Default Associations Configuration File" to "Enabled" poining to \\\\your-domain-sysvol-share-where-your-file-is-hosted\\AppAssociation.xml"
