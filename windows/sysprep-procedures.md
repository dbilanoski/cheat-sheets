---
alias: sysprep
---

# Sysprep Procedures

Generalizing the image removes computer-specific information such as installed drivers and the computer security identifier (SID). You can either use [Sysprep](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview?view=windows-11) by itself or Sysprep with an [unattend](https://learn.microsoft.com/en-us/windows-hardware/customize/desktop/unattend/) answer file to generalize your image and make it ready for deployment.

Mostly this is used for generalizing and pre-configuring images for deployment.

## Preparation

Before standardizing the OS, configure Windows and install software you might want to include.

Best to have it as follows:
- Clean installation of the latest Microsoft Windows version
- Windows update performed to have latest updates fully
- OS settings configured and Windows additional features installed

**Useful batch script from my own environment**

```batch
REM Disable firewalls
netsh advfirewall set allprofiles state off

REM Configure local Administrator password
REM Activating should not be done here as the generalization will disable the account
net user administrator your-prefered- admin-password

REM Configure Power Settings
REM https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/powercfg-command-line-options
REM https://www.windowscentral.com/how-use-powercfg-control-power-settings-windows-10
powercfg /change monitor-timeout-ac 10
powercfg /change monitor-timeout-dc 10
powercfg /change standby-timeout-ac 60
powercfg /change standby-timeout-dc 60
powercfg /setacvalueindex SCHEME_CURRENT 4f971e89-eebd-4455-a8de-9e59040e7347 7648efa3-dd9c-4e3e-b566-50f929386280 3

REM Set KMS server and activate Windows
Slmgr.vbs /skms your-internal-kms-server:1688
Slmgr.vbs /ipk YOUR-VOLUME-KEY
Slmgr.vbs /ato

REM Set the Timezone
tzutil /s "Central Europe Standard Time"

REM Enable additional components which you need
DISM /online /enable-feature /featurename:SmbDirect -ALL
DISM /Online /Enable-Feature /FeatureName:NetFx3 /All 
DISM /online /enable-feature /featurename:NetFx4-AdvSrvs -ALL
DISM /online /enable-feature /featurename:NetFx4Extended-ASPNET45 -ALL
DISM /online /enable-feature /featurename:IIS-ASPNET -ALL
DISM /online /enable-feature /featurename:IIS-ASPNET45 -ALL
DISM /online /enable-feature /featurename:TelnetClient -ALL
DISM /online /enable-feature /featurename:SMB1Protocol -ALL

pause
```


## Starting the syprep generalization

This needs to be executed elevated:

```batch
%WINDIR%\System32\Sysprep\Sysprep.exe /generalize /oobe /shutdown
```

In case you will use [[autoanswer-files]], this is the command:

```batch

%WINDIR%\System32\Sysprep\Sysprep.exe /generalize /oobe /shutdown /unattend:C:\path-to-your-autoanswer-file.xml

```

Where:
* `/generalize` will generalize the OS
* `/oobe` will, after generalization, bring the user to the [Out-of-the-box experience](https://learn.microsoft.com/en-us/windows-hardware/customize/desktop/customize-oobe) 
* `/shutdown` will shut down the computer after the sysprep procedure finishes
* `/unattend` will use provided autoanswer xml file to pre-configure the Windows system


## Troubleshooting & Issues

Sysprep logs are kept here: 

```batch
C:\Windows\System32\Sysprep\Panther
```

where:

* *setupacc.log* contains information about setup actions
* *setuperr.log* contains information about setup errors

On issues with sysprep the procedure will simply exit pointing you to check the logs for details.

### Microsoft.LanguageExperiencePack-GB breaking the sysprep

In case sysprep fails with this error in the *setupacc.log*:

*Error SYSPRP Package Microsoft.LanguageExperiencePack_17134.2.4.0_neutral__8wekyb3d8bbwe was installed for a user, but not provisioned for all users. This package will not function properly in the sysprep image.**

Problematic package needs to be removed for all users, procedure below.

```powershell
# List all packages and locate Microsoft.LanguageExperiencePack-GB
# Copy it's packageFullName string
get-appxpackage -allusers | select name, packagefullname

```

```powershell
remove-appxpackage -allusers -package <paste-the-packagefullname-from-before>
```

Start the sysprep procedure again.

### "BitLocker is on for the OS volume" error

This one happens when the BitLocker is enabled or encryption is in progress. It can also happen when the BitLocker is not enabled but BDE goes to waiting status because it detects the TPM module is active (rare).

To check BitLocker status:
```batch
manage-bde -status
```

To disable BitLocker on the OS drive:

In Powershell

```powershell
Disable-Bitlocker –MountPoint "C:"
```

In Command Prompt
```cmd
manage-bde -off C:
```

In case BitLocker is off and you still get error during sysprep, disable TPM (Trusted Platform Module) in the BIOS. For some devices,  PTT (Intel Platform Trust Technology) also needs to be disabled. 

## References

1. [Official documentation on Sysprep procedure overview](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep-process-overview?view=windows-11)
