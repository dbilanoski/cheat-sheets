---
alias: sysprep
---

# Sysprep Procedures

Generalizing the image removes computer-specific information such as installed drivers and the computer security identifier (SID). You can either use [Sysprep](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview?view=windows-11) by itself or Sysprep with an [unattend](https://learn.microsoft.com/en-us/windows-hardware/customize/desktop/unattend/) answer file to generalize your image and make it ready for deployment.

Mostly this is used for generalizing and preconfiguring images for deployment.

## Preparation

Before standardizing the OS, configure Windows and install sofware you might want to include.

Best to have it as follows:
- Clean installation of the latest Microsoft Windows version
- Windows update performed to have latests updates fully
- OS settings configured and Windows additional features installed

**Useful batch script I generaly use in my enviroment**

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

REM Enable additional components
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

