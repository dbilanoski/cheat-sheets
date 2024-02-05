# Working With Languages In Windows

## Installing Languages

[Install-Language](https://learn.microsoft.com/en-us/powershell/module/languagepackmanagement/install-language?view=windowsserver2022-ps) cmdlet let can be used to install computer wide language pack.

```powershell
Install-language en-GB -CopyToSettings
```

[Set-WinSystemLocale](https://learn.microsoft.com/en-us/powershell/module/international/set-winsystemlocale?view=windowsserver2022-ps) cmdlet can be used to set computer wide system locale (system setting, not user locale).

```powershell
Set-WinSystemLocale en-GB
```

## Computer Settings

These methods can be used.

1. Looking at the registry at "HKLM:\SYSTEM\CurrentControlSet\Control\Nls\Language"
   
   ```powershell
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Nls\Language"
   ```
   
   these are of importance:
   
   ```
   InstallLanguage         : # 0809 --> Installed during setup
   InstallLanguageFallback : # {en-US} --> Language that is used for resources that are not localized for the default system user interface (UI)
   Default                 : # 0809 --> Set by Regional Control Panel, Administrative tab, Language for non-Unicode Programs
   ```
   
   Example:
   ```powershell
   (Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Nls\Language").Default # Returns currently default language
   ```
   

2. Using the DISM module
   
   ```cmd
   dism /online /get-intl
   ```
   
   which will return:
   
   ```text
   Default system UI language : en-GB
   The UI language fallback is : en-US
   System locale : en-GB
   Default time zone : Central Europe Standard Time
   Active keyboard(s) : 0809:0000041a
   Keyboard layered driver : PC/AT Enhanced Keyboard (101/102-Key)
   
   Installed language(s): en-GB
   Type : Partially localized language, MUI type.
   Fallback Languages en-US
   Installed language(s): en-US
   Type : Fully localized language.
   ```
  

## Current User Settings

In most cases, this is where changes will be made.

To check current settings, use [Get-WinUserLanguageList](https://learn.microsoft.com/en-us/powershell/module/international/get-winuserlanguagelist?view=windowsserver2022-ps) cmdlet 
   
   ```powershell
   Get-WinUserLanguageList
   ```
   
   ```text
   LanguageTag     : en-GB
   Autonym         : English (United Kingdom)
   EnglishName     : English
   LocalizedName   : English (United Kingdom)
   ScriptName      : Latin
   InputMethodTips : {0809:0000041A} --> This is keyboard layout
   Spellchecking   : True
   Handwriting     : False
   ```

To set configuration to current user, [Set-WinUserLanguageList](https://learn.microsoft.com/en-us/powershell/module/international/set-winuserlanguagelist?view=windowsserver2022-ps) can be used.

```powershell
Set-WinUserLanguageList en-GB -Force # Will set en-GB as only language to be used
```

Note - do set only installed languages.


## Scripts

Install new language and set it to be used (needs reboot).

```powershell
Install-language en-GB -CopyToSettings
Set-WinSystemLocale en-GB
Set-WinUserLanguageList en-GB -Force
Set-SystemPreferredUILanguage -Language en-GB
```


Change keyboard layout for current language:

```powershell
# Get currently installed languages
$languages = Get-WinUserLanguageList
# Clear currently installed keyboard layouts
$languages[0].InputMethodTips.Clear()
# Set inputpMethodTip (keyboard layout) to desired value
$languages[0].InputMethodTips.Add('0809:0000041A') # This sets en-GB with Croatian keyboard layout
# Apply changes to the language settings
Set-WinUserLanguageList $languages -Force

# Optional - set this layout as default to current user
Set-WinDefaultInputMethodOverride -InputTip "0809:0000041A"
```


## References

1. [Available Input Locales Official Docs](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/default-input-locales-for-windows-language-packs?view=windows-11)