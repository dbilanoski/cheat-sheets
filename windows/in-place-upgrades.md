---
aliases:
  - windows upgrade
  - windows 10 to windows 11
  - silent upgrade
---

# In-place Upgrade Scripts

Here we are looking at upgrading Windows to next version (11 in this case) by using locally prepared ISO content to upgrade computers remotely and silently while keeping user data and computer  configuration.

Approaches which might show up in the script examples:
* Mounting Windows 11 ISO file from a network share
* Directly executing extracted content of an Windows 11 ISO file from a network share
* Downloading Windows 11 ISO to a computer then using it locally either mounted or extracted
* Using Windows upgrade assistant application to trigger the update remotely


## Scripts

### Basic Off-site upgrade with extracted ISO

This will download extracted ISO hosted somewhere, extract it locally on computer and then execute the upgrade procedure.

```powershell
<#

.DESCRIPTION

  Used to trigger upgrade procedure from Windows 10 to Windows 11 by downloading ISO package from online repository then executing the procedure locally.

  Before usage, adjust path and arguments variables according to your environment and needs.

  Windows setup.exe cli arguments: https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-command-line-options?view=windows-11#skipfinalize

  Exit codes: 1 means Windows 11 is already installed. 2 means issue with downloading or extaction of ISO content.

.NOTES

  Version:        1.0
  Author:         Danilo Bilanoski
  Creation Date:  22/11/2023
  Purpose/Change: Initial script development

.EXAMPLE

  Execute from elevated cmd as: powershell -executionpolicy bypass -file "\\path-to-this-script\get-win11upgrade-offsite.ps1"

#>

# Check if Windows 11 is already installed
if((Get-WmiObject Win32_OperatingSystem).Caption -Match "Windows 11") {

  Write-Error "Computer already on Windows 11."
  exit 1

}

# Set standby and hibernate timers to 0
powercfg /change standby-timeout-ac 0
powercfg /change standby-timeout-dc 0
powercfg /change hibernate-timeout-ac 0
powercfg /change hibernate-timeout-ac 0

# Configure urls, paths and installation arguments
$source = "https://your-url-to-zipped-iso\Win11_23H2_EnglishInternational_x64.zip"
$destination = "C:\Users\Public\Downloads"
$extract = "C:\temp"
$setup = "C:\temp\Win11_23H2_EnglishInternational_x64\setup.exe"
$logsdir = "c:\temp\logs\win11"
# Add /noreboot if you want to leave first reboot to the user
$arguments = "/Auto Upgrade /Quiet /migratedrivers all /ShowOOBE none /Compat IgnoreWarning /Telemetry Disable /DynamicUpdate disable /eula accept /BitLocker AlwaysSuspend /copylogs $logsdir"

# Download ISO package
(New-Object System.Net.WebClient).DownloadFile($source, $destination)

# Extract ISO package
if(Test-Path -Path $destination) {

  Expand-Archive -LiteralPath $destination -DestinationPath $extract

} else {

  Write-error "Download was not succesfull."
  Exit 2

} 

# Execute installer
if(Test-Path -Path $setup) {

  Start-Process -NoNewWindow -FilePath "$($setup)" -ArgumentList $arguments -Wait

} else {

  Write-error "Extracting from downloaded zip archive was not sucessfull."
  Exit 2
  
}
```


### Basic On-site upgrade with extracted ISO

This will directly execute extracted ISO contents from a network share to perform an upgrade. Suitable for on-site computers where network shares are available over high-speed internal links. Alternatively, content can be first downloaded to each machine before execution.

```powershell
<#

.DESCRIPTION

  Used to trigger upgrade procedure from Windows 10 to Windows 11 using locally hosted contents of an Windows 11 installation ISO file.

  Before usage, adjust path and arguments variables according to your environment and needs.

  Windows setup.exe cli arguments: https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-command-line-options?view=windows-11
 
 Exit codes: 1 means Windows 11 is already installed.

.NOTES

  Version:        1.0
  Author:         Danilo Bilanoski
  Creation Date:  22/11/2023
  Purpose/Change: Initial script development

.EXAMPLE

  Execute from elevated cmd as: powershell -executionpolicy bypass -file "\\path-to-this-script\get-win11upgrade-onsite.ps1"

#>

# Check if Windows 11 is already installed
if((Get-WmiObject Win32_OperatingSystem).Caption -Match "Windows 11") {

  Write-Error "Computer already on Windows 11."
  exit 1

}

# Set standby and hibernate timers to 0 (Uncomment if needed)
# powercfg /change standby-timeout-ac 0
# powercfg /change standby-timeout-dc 0
# powercfg /change hibernate-timeout-ac 0
# powercfg /change hibernate-timeout-ac 0
# powercfg /change disk-timeout-ac 0
# powercfg /change disk-timeout-dc 0

# Configure paths and installation arguments
$setup = "\\your-path-to-extracted-setup\setup.exe"
$logsdir = "c:\temp\logs\win11"
# Add /noreboot if you want to leave first reboot to the user
$arguments = "/Auto Upgrade /Quiet /migratedrivers all /ShowOOBE none /Compat IgnoreWarning /Telemetry Disable /DynamicUpdate disable /eula accept /BitLocker AlwaysSuspend /copylogs $logsdir"

# Execute installer
Start-Process -NoNewWindow -FilePath "$($setup)" -ArgumentList $arguments -Wait
```

### Useful additions you might need 

Additional configuration you might need in your scripts for better automatization and coverage.

#### Set standby and hibernate timers to 0

```powershell
powercfg /change standby-timeout-ac 0
powercfg /change standby-timeout-dc 0
powercfg /change hibernate-timeout-ac 0
powercfg /change hibernate-timeout-ac 0
powercfg /change disk-timeout-ac 0
powercfg /change disk-timeout-dc 0
```

#### Filter targets by computer model to exclude unsupported computers

```powershell
# Filter targets by computer models to exclude unsupported computers
$unsupported_models = @("HP ProDesk 600 G3 SFF","HP EliteDesk 800 G2 SFF")
$current_model = (Get-CimInstance -ClassName Win32_ComputerSystem).Model

if($current_model -in $unsupported_models) {
  Write-Error "Computer model: $computer_model. Windows 11 not supported here."
  exit
}
```

#### Disable / Bypass internal WSUS
```powershell
# Disable internal update server usage
Set-ItemProperty -Path "HKLM:Software\Policies\Microsoft\Windows\WindowsUpdate\AU" -Name "UseWUServer" -Value 0
Restart-Service -Name wuauserv -Force

# Set optional features to be downloaded from Windows Update so language download becomes accessible (might not be necesarry if "Disable internal update server usage" from earlier worked)

if(-not(Test-Path "HKLM:Software\Microsoft\Windows\CurrentVersion\Policies\Servicing"){   New-Item -Path "HKLM:Software\Microsoft\Windows\CurrentVersion\Policies\Servicing"
}
New-ItemProperty -Path "HKLM:Software\Microsoft\Windows\CurrentVersion\Policies\Servicing" -Name "RepairContentServerSource" -Value 2
Restart-Service -Name wuauserv -Force

```

#### Stop Symantec Endpoint Protection service

```powershell
# This will autoresolve path regardless of the build number inside 14.3 version. Adjust this number for your environment
Start-Process -FilePath (Resolve-Path -Path 'C:\Program Files\Symantec\Symantec Endpoint Protection\14.3.*\Bin64\Smc.exe')[-1].path -ArgumentList '-stop -p Tr4nsc0m123!' -NoNewWindow -Wait
```

#### Check and install languages
```powershell
# Say we want en-GB to be used as we deploy that ISO. In case it's not there, here's how to fix it while preseving user's current keyboard layout

$languages = Get-WinUserLanguageList
$keyboardLayout = $languages[0].InputMethodTips.split(":")

if (-Not ($languages[0].LanguageTag -eq "en-GB")) {
  # Get all installed langs in an workable array
  [string[]]$langs = (get-winuserlanguagelist).LanguageTag

  # Check if en-GB is already installed
  if (-not ("en-GB" -in $langs)) {

    # Set optional features to be downloaded from Windows Update so language download becomes accessible
    if(-not(Test-Path "HKLM:Software\Microsoft\Windows\CurrentVersion\Policies\Servicing")) {
      New-Item -Path "HKLM:Software\Microsoft\Windows\CurrentVersion\Policies\Servicing"
    }
    New-ItemProperty -Path "HKLM:Software\Microsoft\Windows\CurrentVersion\Policies\Servicing" -Name "RepairContentServerSource" -Value 2
    Restart-Service -Name wuauserv -Force

    # Install language and update user language list
    Install-language en-GB -CopyToSettings
    Set-WinUserLanguageList en-GB -Force
    $languages = Get-WinUserLanguageList
  }

  # Configure language and keybord layout
  Set-WinSystemLocale en-GB
  # Clear existing keyboard layouts (just in case)
  $languages[0].InputMethodTips.Clear()
  # Add previously configured keyboard layout to new language
  $languages[0].InputMethodTips.Add("0809:$($keyboardLayout)")
  Set-WinUserLanguageList $languages -Force
  # Schedule reboot
  shutdown /r /f /t 60
  Write-Error "Switching to en-GB language and rebooting the computer."
  Exit
}
```
## Issues & Troubleshooting

Installation and error logs are kept by default in the hidden folder `C:\$Windows.~BT\Sources\Panther`. Scripts above will copy those to the `$logsdir` variable.

Due to the cryptic nature of log entries you might have more success by executing the setup.exe with the graphical interface on problematic computers to see more understandable error message. Just remove `/quiet` argument end execute setup.exe with rest of your arguments.
### Script hangs indefinitely on newest ISO files with "Appraiser" or "CONX   aeinv:" errors

On Windows ISO media newer then July 2023. (and December 2023. in the moment of writing), upgrade script might hang indefinitely or get stuck on around 13% if graphical interface was used. 

If you were to check the upgrade logs in $Windows.~BT\Sources\Panther

- setupact.log might hold few of these: "Info CONX Windows::Compat::Appraiser::WuDriverCoverageDataSource::PrefetchData (699): Using WU cache [NI22H2]"
- setuperr.log will have few of these: " Problem with "CONX   aeinv: ERROR,Application::Retrieve,2320,AmiUtilityRegGetValue failed [2]"

Problem is newer ISO versions come with upgrade related bugs by shipping faulty **acmigration.dll**.

#### Solution

1. Use older ISOs than July 2023 or
2. If you are to fix this on newer ISO, latest kb upgrade package file containing the working  acmigration.dll is KB5028314

- Download  [KB5028314](https://www.catalog.update.microsoft.com/Search.aspx?q=KB5028314)
- Extract acmigration.dll from cab package and replace it in your extracted ISO folder under \..\sources\ 
- Restart the script and try again

Highest working version of acmigration.dll is: 10.0.22621.1986

Additionally,  **appraiserres.dll** might also hold issues and might need to be replaced but I did not encounter it.

#### References

1.  [Good read on Reddit](https://www.reddit.com/r/SCCM/comments/15tutvf/in_place_upgrade_hanging_recent/)
2. [Another good read on Reddit](https://www.reddit.com/r/SCCM/comments/17pxxvv/23h2_inplace_upgrade_stuck_at_14/)
3. [One more Reddit read just for a good measure](https://www.reddit.com/r/techsupport/comments/17c7ypq/windows_11_deployment_error_amiutilityreggetvalue/)


### "MigChoice: Selected install choice is not available" Error

Script exits in the first minute of the procedure and setuperr.log will hold this one:

```text
2023-12-21 14:32:48, Error                 MOUPG  MigChoice: Selected install choice is not available.
```

In case you executed graphical interface, it will ask you how to handle user data and installed applications with option to "Keep installed applications" greyed out. This is because the language of the installer package does not correspond to the installed computer UI language.

#### Solution

Either download ISO with language you already use on computers or [[languages-keyboard-layouts|install language]] of the ISO you already have. To install the language, use this script (needs reboot):

```powershell
Install-language en-GB -CopyToSettings
Set-WinSystemLocale en-GB
Set-WinUserLanguageList en-GB -Force
# shutdown /r /f /t 60 # For reboot scheduling
```
### "We couldn't update the system reserved partition" Error

Executing setup.exe with GUI returns this error on the beginning of the procedure or the installer might exit with error code 0xc1900104, or error code 0x800f0922, or in logs, you might see something like this:

```text
2023-12-17 16:24:55, Error                 CONX   Appraiser: ERROR,SdbpGetManifestedMergeStubAlloc,1017,SdbpGetMergeSdbsDisabled failed [c0000034]

2023-12-17 16:25:02, Error                 MOUPG  CSetupResponseTemplate<class IDlpResponse>::OnCancel(238): Result = 0x800704C7
2023-12-17 16:25:02, Error                 MOUPG  CSetupResponseTemplate<class IDlpResponse>::ExecuteRoutine(143): Result = 0x800704C7
2023-12-17 16:25:02, Error                 MOUPG  CDlpResponseImpl<class CDlpErrorImpl<class CDlpObjectInternalImpl<class CUnknownImpl<class IDlpResponse> > > >::Execute(1923): Result = 0x800704C7
2023-12-17 16:25:02, Error                 MOUPG  CDlpActionImpl<class CDlpErrorImpl<class CDlpObjectInternalImpl<class CUnknownImpl<class ICompatAction> > > >::ExecuteResponse(1365): Result = 0x800704C7
2023-12-17 16:25:02, Error                 MOUPG  CDlpActionCompat::ExecuteSysReqScan(809): Result = 0xC1900201
2023-12-17 16:25:02, Error                 MOUPG  CDlpActionCompat::ExecuteRoutine(638): Result = 0xC1900201
2023-12-17 16:25:02, Error                 MOUPG  CDlpActionImpl<class CDlpErrorImpl<class CDlpObjectInternalImpl<class CUnknownImpl<class ICompatAction> > > >::Execute(493): Result = 0xC1900201
2023-12-17 16:25:02, Error                 MOUPG  CDlpTask::ExecuteAction(3300): Result = 0xC1900201
2023-12-17 16:25:02, Error                 MOUPG  CDlpTask::ExecuteActions(3454): Result = 0xC1900201
2023-12-17 16:25:02, Error                 MOUPG  CDlpTask::Execute(1631): Result = 0xC1900201
```

This can mean one of two things - the System Reserved Partition (SRP) may be full or might be running with underlying issues. The System Reserve Partition (SRP) is a small partition on your hard drive that stores boot information for Windows.

#### Solution

1. Check if your SRP partition is full, and what kind of partition is it (MBR or GPT).
   
   Elevated CMD > diskmgmt.exe > Right click the disk where the OS is located > Select "Properties" > Click "Volumes" tab > Check "Partition Style" (MBR or GPT) and take a note of it

2. Back in the Disk Management, find "EFI System Partition" if GPT partition style, if MBR, then it might be called "System Reserved". Confirm if it's full or nearly full. Installation procedure will need around 15 MB of free space.
   
3. Now we free some space. **Be extra cautions what you delete here** as you can mess the boot procedure and end up with non-bootable computer.  First mount the SRP partition and assign it a letter so it can be accessed:
   ```cmd
   mountvol y: /s
	```

4. Then navigate to the Fonts directory as that one is never used and safe to delete and delete contents of it.
   ```cmd
   cd y:
   cd EFI\Microsoft\Boot\Fonts
   del *.*
	```
	Confirm the deletion. 

5. Check disk space and try running installation again.
   ```cmd
   dir y:
	```

In case there is less than 15 MB free, you can try deleting unused language directories under `Y:\EFI\Microsoft\Boot` but at this point, clean installation might be better approach.

```cmd
cd y:
cd EFI\Microsoft\Boot
rmdir /s /q da-DK el-GR es-ES es-MX et-EE fi-FI fr-CA fr-FR hu-HU it-IT ja-JP ko-KR lt-LT lv-LV nb-NO nl-NL pl-PL pt-PT rp-RO ru-RU sk-SK sl-SI sv-SE tr-TR zh-CN
```


## References

1. [Windows 11 setup.exe command line options](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-command-line-options?view=windows-11)
2. [Powercfg command line options](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/powercfg-command-line-options#option_change)