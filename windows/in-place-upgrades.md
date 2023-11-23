---
aliases:
  - windows upgrade
  - windows 10 to windows 11
  - silent upgrade
---

# In-place Upgrade Scripts

Here we are looking at upgrading Windows to next version (11 in this case) by using locally prepared ISO file/content to upgrade computers remotely and silently while keeping user data and computer  configuration.

Approaches which might show up in the script examples:
* Mounting Windows 11 ISO file from a network share
* Directly executing extracted content of an Windows 11 ISO file from a network share
* Downloading Windows 11 ISO to a computer then using it locally either mounted or extracted
* Using Windows upgrade assistant application to trigger the update remotely


## Scripts

### Off-site upgrade with extracted ISO

This will download extracted ISO hosted somewhere, extract it locally on computer and then execute the upgrade procedure.

```powershell
<#

.DESCRIPTION

  Used to trigger upgrade procedure from Windows 10 to Windows 11 by downloading ISO package from online repository then executing the procedure locally.

  Before usage, adjust path and arguments variables according to your environment and needs.

  Windows setup.exe cli arguments: https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-command-line-options?view=windows-11#skipfinalize

  Exit codes: 1 means Windows 11 is already installed. 2 means issue with downloading or extaction ISO content.

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
powercfg /change disk-timeout-ac 0
powercfg /change disk-timeout-dc 0



# Configure urls, paths and installation arguments
$source = "https://your-url-to-zipped-iso\Win11_23H2_EnglishInternational_x64.zip"
$extract = "C:\temp"
$setup = "C:\temp\Win11_23H2_EnglishInternational_x64\setup.exe"
$logsdir = "c:\temp\logs\win11"
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


### On-site upgrade with extracted ISO

This will directly execute extracted ISO contents from a network share to perform an upgrade. Suitable for on-site computers where network shares are available over high-speed internal links. Alternatively, content can be first downloaded to each machine before execution.

```powershell
<#

.DESCRIPTION

  Used to trigger upgrade procedure from Windows 10 to Windows 11 using locally hosted contents of an Windows 11 installation ISO file.

  Before usage, adjust path and arguments variables according to your environment and needs.

  Windows setup.exe cli arguments: https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-command-line-options?view=windows-11

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
  exit 0

}

# Set standby and hibernate timers to 0
powercfg /change standby-timeout-ac 0
powercfg /change standby-timeout-dc 0
powercfg /change hibernate-timeout-ac 0
powercfg /change hibernate-timeout-ac 0
powercfg /change disk-timeout-ac 0
powercfg /change disk-timeout-dc 0

# Configure paths and installation arguments
$setup = "\\your-path-to-extracted-setup\setup.exe"
$logsdir = "c:\temp\logs\win11"
$arguments = "/Auto Upgrade /Quiet /migratedrivers all /ShowOOBE none /Compat IgnoreWarning /Telemetry Disable /DynamicUpdate disable /eula accept /BitLocker AlwaysSuspend /copylogs $logsdir"

# Execute installer
Start-Process -NoNewWindow -FilePath "$($setup)" -ArgumentList $arguments -Wait
```

## Issues & Troubleshooting

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

## References

1. [Windows 11 setup.exe command line options](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-command-line-options?view=windows-11)
2. [Powercfg command line options](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/powercfg-command-line-options#option_change)