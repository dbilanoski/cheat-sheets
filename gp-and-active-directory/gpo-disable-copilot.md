# Disabling Copilot Via GPO

## GPO Configuration

Go to `User Configuration > Administrative Templates > Windows Components > Windows Copilot` and set `Turn off Windows Copilot` to `Enabled`

Expected results:
* Copilot button will be removed from taskbar
* "Windows key + c" shortcut will no longer work
* Copilot settings will disappear from Taskbar settings menu

To re-enable it, set policy to "Not configured".


## Registry Configuration

These keys are related to the Copilot. Value "1" will disable it.

Current User Settings
`[HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\WindowsCopilot] "TurnOffWindowsCopilot"=dword:00000001`

Local Machine Settings
`[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot] "TurnOffWindowsCopilot"=dword:00000001`

These can be saved as a `.reg` file and imported.

## Powershell Approach

To disable it via powershell script first create key, then configure name-value pair.

```powershell
# Create Key
New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\WindowsCopilot"  
# Set "TurnOffWindowsCopilot" with value "1"
Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\WindowsCopilot" -Name "TurnOffWindowsCopilot" -Value 1
```

## Re-enabling Copilot

Setting GPO policy as "Not configured" or deleting those two registry keys if that method was used and rebooting the machine afterwards is usually enough to get the feature back.

Otherwise, these methods can be tried.

### Configure CopilotBingChat Eligibility

In registry, set `[HKEY_CURRENT_USER\Software\\Microsoft\Windows\Shell\Copilot\BingChat] "IsUserEligible"=dword:00000001

```powershell
Set-ItemProperty -Path "HKCU:\Software\\Microsoft\Windows\Shell\Copilot\BingChat" -name "IsUSerEligible" -value 1
```
  
### Configure Copilot Button Visibility

In registry, set `[HKEY_CURRENT_USERSoftware\Microsoft\Windows\CurrentVersion\Explorer\Advanced] "ShowCopilotButton"=dword:00000001

```powershell
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced\" -name ShowCopilotButton -value 1  
```


### Execute Copilot From Run (Or Create Shortcut)

Click run and execute this: `microsoft-edge://?ux=copilot&tcp=1&source=taskbar`