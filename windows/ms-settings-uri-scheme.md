# Windows Settings App With MS-Settings URI Scheme

In Windows, there is a method to access specific Windows Settings pages directly using the `ms-settings` uniform resource identifier ([URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)) scheme.

When a command using that scheme is executed,  “_SystemSettings.exe”_ application located in `C:\Windows\ImmersiveControlPanel` will launch and interpret the URI structure bringing the correct settings page, fetching the configuration data from other places and rendering the page itself.
## How To Run MS-Settings URI Links

Syntax: `**ms-settings:uri-parameter**`  is used as a command from any of the following locations:

1. From the **Run dialog** (Windows key + R)
2. From the **File Explorer address bar** (Windows key + E)
3. From command-line interfaces such as **CMD or PowerShell**
4. By [creating a **Windows shortcut**](https://medium.com/@dbilanoski/how-to-tuesdays-shortcuts-with-powershell-how-to-make-customize-and-point-them-to-places-1ee528af2763)

**Examples with Network > Proxy Settings page**

```powershell
# From Run dialog, File Explorer's address bar or as a target path in Windows shortcut  
ms-settings:network-proxy  
  
# From Command Prompt (CMD)  
start ms-settings:network-proxy  
  
# From PowerShell  
start-process ms-settings:network-proxy
```

## Complete List Of Windows 11 MS-Settings URI Commands

|Category          |Settings Page                       |URI Command                                                  |
|------------------|------------------------------------|-------------------------------------------------------------|
|Accounts          |Access work or school               |ms-settings:workplace                                        |
|Accounts          |Email & app accounts                |ms-settings:emailandaccounts                                 |
|Accounts          |Family & other people               |ms-settings:otherusers                                       |
|Accounts          |Set up a kiosk                      |ms-settings:assignedaccess                                   |
|Accounts          |Sign-in options                     |ms-settings:signinoptions                                    |
|Accounts          |Sync your settings                  |ms-settings:sync                                             |
|Accounts          |Windows Hello setup                 |ms-settings:signinoptions-launchfaceenrollment               |
|Accounts          |Your info                           |ms-settings:yourinfo                                         |
|Apps              |Apps & Features                     |ms-settings:appsfeatures                                     |
|Apps              |Apps for websites                   |ms-settings:appsforwebsites                                  |
|Apps              |Default apps                        |ms-settings:defaultapps                                      |
|Apps              |Manage optional features            |ms-settings:optionalfeatures                                 |
|Apps              |Offline Maps                        |ms-settings:maps                                             |
|Apps              |Startup apps                        |ms-settings:startupapps                                      |
|Apps              |Video playback                      |ms-settings:videoplayback                                    |
|Control Center    |Control center                      |ms-settings:controlcenter                                    |
|Cortana           |Cortana across my devices           |ms-settings:cortana-notifications                            |
|Cortana           |More details                        |ms-settings:cortana-moredetails                              |
|Cortana           |Permissions & History               |ms-settings:cortana-permissions                              |
|Cortana           |Searching Windows                   |ms-settings:cortana-windowssearch                            |
|Cortana           |Talk to Cortana                     |ms-settings:cortana                                          |
|Devices           |AutoPlay                            |ms-settings:autoplay                                         |
|Devices           |Bluetooth                           |ms-settings:bluetooth                                        |
|Devices           |Connected Devices                   |ms-set                                                       |
|Devices           |Camera settings                     |ms-settings:camera                                           |
|Devices           |Mouse & touchpad                    |ms-settings:mousetouchpad                                    |
|Devices           |Pen & Windows Ink                   |ms-settings:pen                                              |
|Devices           |Printers & scanners                 |ms-settings:printers                                         |
|Devices           |Touch                               |ms-settings:devices-touch                                    |
|Devices           |Touchpad                            |ms-settings:devices-touchpad                                 |
|Devices           |Text Suggestions                    |ms-settings:devicestyping-hwkbtextsuggestions                |
|Devices           |Typing                              |ms-settings:typing                                           |
|Devices           |USB                                 |ms-settings:usb                                              |
|Devices           |Wheel                               |ms-settings:wheel                                            |
|Devices           |Your phone                          |ms-settings:mobile-devices                                   |
|Ease Of Access    |Display                             |ms-settings:easeofaccess-display                             |
|Ease Of Access    |Eye control                         |ms-settings:easeofaccess-eyecontrol                          |
|Ease Of Access    |Fonts                               |ms-settings:fonts                                            |
|Ease Of Access    |High contrast                       |ms-settings:easeofaccess-highcontrast                        |
|Ease Of Access    |Keyboard                            |ms-settings:easeofaccess-keyboard                            |
|Ease Of Access    |Magnifier                           |ms-settings:easeofaccess-magnifier                           |
|Ease Of Access    |Mouse                               |ms-settings:easeofaccess-mouse                               |
|Ease Of Access    |Mouse pointer & touch               |ms-settings:easeofaccess-mousepointer                        |
|Ease Of Access    |Narrator                            |ms-settings:easeofaccess-narrator                            |
|Ease Of Access    |Color filters                       |ms-settings:easeofaccess-colorfilte                          |
|Ease Of Access    |Speech                              |ms-settings:easeofaccess-speechrecognition                   |
|Ease Of Access    |Text cursor                         |ms-settings:easeofaccess-cursor                              |
|Ease Of Access    |Visual Effects                      |ms-settings:easeofaccess-visualeffects                       |
|Ease Of Access    |Audio                               |ms-settings:easeofaccess-audio                               |
|Ease Of Access    |Closed captions                     |ms-settings:easeofaccess-closedcaptioning                    |
|Extras            |Extras                              |ms-settings:extras                                           |
|Family            |Family                              |ms-settings:family-group                                     |
|Gaming            |Game bar                            |ms-settings:gaming-gamebar                                   |
|Gaming            |Game DVR                            |ms-settings:gaming-gamedvr                                   |
|Gaming            |Game Mode                           |ms-settings:gaming-gamemode                                  |
|Gaming            |Playing a game full screen          |ms-settings:quietmomentsgame                                 |
|Mixed Reality     |Audio and speech                    |ms-settings:holographic-audio                                |
|Mixed Reality     |Environment                         |ms-settings:privacy-holographic-environment                  |
|Mixed Reality     |Headset display                     |ms-settings:holographic-headset                              |
|Mixed Reality     |Uninstall                           |ms-settings:holographic-management                           |
|Mixed Reality     |Startup and desktop                 |ms-settings:holographic-startupandesktop                     |
|Network & Internet|Network & internet                  |ms-settings:network-status                                   |
|Network & Internet|Advanced settings                   |ms-settings:network-advancedsettings                         |
|Network & Internet|Airplane mode                       |ms-settings:network-airplanemode                             |
|Network & Internet|Cellular & SIM                      |ms-settings:network-cellular                                 |
|Network & Internet|Dial-up                             |ms-settings:network-dialup                                   |
|Network & Internet|DirectAccess                        |ms-settings:network-directaccess                             |
|Network & Internet|Ethernet                            |ms-settings:network-ethernet                                 |
|Network & Internet|Manage known networks               |ms-settings:network-wifisettings                             |
|Network & Internet|Mobile hotspot                      |ms-settings:network-mobilehotspot                            |
|Network & Internet|Proxy                               |ms-settings:network-proxy                                    |
|Network & Internet|VPN                                 |ms-settings:network-vpn                                      |
|Network & Internet|Wi-Fi                               |ms-settings:network-wifi                                     |
|Network & Internet|Wi-Fi provisioning                  |ms-settings:wifi-provisioning                                |
|Personalization   |Background                          |ms-settings:personalization-background                       |
|Personalization   |Choose which folders appear on Start|ms-settings:personalization-start-places                     |
|Personalization   |Colors                              |ms-settings:colors                                           |
|Personalization   |Lock screen                         |ms-settings:lockscreen                                       |
|Personalization   |Personalization (category)          |ms-settings:personalization                                  |
|Personalization   |Start                               |ms-settings:personalization-start                            |
|Personalization   |Taskbar                             |ms-settings:taskbar                                          |
|Personalization   |Touch Keyboard                      |ms-settings:personalization-touchkeyboard                    |
|Personalization   |Themes                              |ms-settings:themes                                           |
|Phone             |Your phone                          |ms-settings:mobile-devices                                   |
|Phone             |Device Usage                        |ms-settings:deviceusage                                      |
|Privacy           |Account info                        |ms-settings:privacy-accountinfo                              |
|Privacy           |Activity history                    |ms-settings:privacy-activityhistory                          |
|Privacy           |App diagnostics                     |ms-settings:privacy-appdiagnostics                           |
|Privacy           |Automatic file downloads            |ms-settings:privacy-automaticfiledownloads                   |
|Privacy           |Background Apps                     |ms-settings:privacy-backgroundapps                           |
|Privacy           |Background Spatial Perception       |ms-settings:privacy-backgroundspatialperception              |
|Privacy           |Calendar                            |ms-settings:privacy-calendar                                 |
|Privacy           |Call history                        |ms-settings:privacy-callhistory                              |
|Privacy           |Camera                              |ms-settings:privacy-webcam                                   |
|Privacy           |Contacts                            |ms-settings:privacy-contacts                                 |
|Privacy           |Documents                           |ms-settings:privacy-documents                                |
|Privacy           |Downloads folder                    |ms-settings:privacy-downloadsfolder                          |
|Privacy           |Email                               |ms-settings:privacy-email                                    |
|Privacy           |Eye tracker                         |ms-settings:privacy-eyetracker (requires eyetracker hardware)|
|Privacy           |Feedback & diagnostics              |ms-settings:privacy-feedback                                 |
|Privacy           |File system                         |ms-settings:privacy-broadfilesystemaccess                    |
|Privacy           |General                             |ms-settings:privacy or ms-settings:privacy-general           |
|Privacy           |Graphics                            |ms-settings:privacy-graphicscaptureprogrammatic              |
|Privacy           |Inking & typing                     |ms-settings:privacy-speechtyping                             |
|Privacy           |Location                            |ms-settings:privacy-location                                 |
|Privacy           |Messaging                           |ms-settings:privacy-messaging                                |
|Privacy           |Microphone                          |ms-settings:privacy-microphone                               |
|Privacy           |Motion                              |ms-settings:privacy-motion                                   |
|Privacy           |Music Library                       |ms-settings:privacy-musiclibrary                             |
|Privacy           |Notifications                       |ms-settings:privacy-notifications                            |
|Privacy           |Other devices                       |ms-settings:privacy-customdevices                            |
|Privacy           |Phone calls                         |ms-settings:privacy-phonecalls                               |
|Privacy           |Pictures                            |ms-settings:privacy-pictures                                 |
|Privacy           |Radios                              |ms-settings:privacy-radios                                   |
|Privacy           |Speech                              |ms-settings:privacy-speech                                   |
|Privacy           |Tasks                               |ms-settings:privacy-tasks                                    |
|Privacy           |Videos                              |ms-settings:privacy-videos                                   |
|Privacy           |Voice activation                    |ms-settings:privacy-voice                                    |
|Search            |Search                              |ms-settings:search                                           |
|Search            |Search more details                 |ms-settings:search-moredetails                               |
|Search            |Search Permissions                  |ms-settings:search-permissions                               |
|Surface Hub       |Accounts                            |ms-settings:surfacehub-accounts                              |
|Surface Hub       |Session cleanup                     |ms-settings:surfacehub-sessioncleanup                        |
|Surface Hub       |Team Conferencing                   |ms-settings:surfacehub-calling                               |
|Surface Hub       |Team device management              |ms-settings:surfacehub-devicemanagenent                      |
|Surface Hub       |Welcome screen                      |ms-settings:surfacehub-welcome                               |
|System            |About                               |ms-settings:about                                            |
|System            |Advanced display settings           |ms-settings:display-advanced                                 |
|System            |App volume and device preferences   |ms-settings:apps-volume                                      |
|System            |Battery Saver                       |ms-settings:batterysaver                                     |
|System            |Battery Saver settings              |ms-settings:batterysaver-settings                            |
|System            |Battery use                         |ms-settings:batterysaver-usagedetails                        |
|System            |Clipboard                           |ms-settings:clipboard                                        |
|System            |Display                             |ms-settings:display                                          |
|System            |Default Save Locations              |ms-settings:savelocations                                    |
|System            |Display                             |ms-settings:screenrotation                                   |
|System            |Duplicating my display              |ms-settings:quietmomentspresentation                         |
|System            |During these hours                  |ms-settings:quietmomentsscheduled                            |
|System            |Encryption                          |ms-settings:deviceencryption                                 |
|System            |Energy recommendatations            |ms-settings:energyrecommendations                            |
|System            |Focus assist                        |ms-settings:quiethours                                       |
|System            |Graphics Settings                   |ms-settings:display-advancedgraphic                          |
|System            |Graphics Default Settings           |ms-settings:display-advancedgraphics-default                 |
|System            |Multitasking                        |ms-settings:multitasking                                     |
|System            |Night light settings                |ms-settings:nightlight                                       |
|System            |Projecting to this PC               |ms-settings:project                                          |
|System            |Shared experiences                  |ms-settings:crossdevice                                      |
|System            |Taskbar                             |ms-settings:taskbar                                          |
|System            |Notifications & actions             |ms-settings:notifications                                    |
|System            |Remote Desktop                      |ms-settings:remotedesktop                                    |
|System            |Power & sleep                       |ms-settings:powersleep                                       |
|System            |Presence sensing                    |ms-settings:presence                                         |
|System            |Sound                               |ms-settings:sound                                            |
|System            |Sound devices                       |ms-settings:sound-devices                                    |
|System            |Storage                             |ms-settings:storagesense                                     |
|System            |Storage Sense                       |ms-settings:storagepolicies                                  |
|System            |Storage recommendations             |ms-settings:storagerecommendations                           |
|System            |Disks & volumes                     |ms-settings:disksandvolumes                                  |
|Time & Language   |Date & time                         |ms-settings:dateandtime                                      |
|Time & Language   |Region                              |ms-settings:regionformatting                                 |
|Time & Language   |Language                            |ms-settings:keyboard                                         |
|Time & Language   |Speech                              |ms-settings:speech                                           |
|Time & Language   |Add display language                |ms-settings:regionlanguage-adddisplaylanguage                |
|Time & Language   |Language options                    |ms-settings:regionlanguage-languageoptions                   |
|Time & Language   |Set display language                |ms-settings:regionlanguage-setdisplaylanguage                |
|Update & Security |Activation                          |ms-settings:activation                                       |
|Update & Security |Backup                              |ms-settings:backup                                           |
|Update & Security |Delivery Optimization               |ms-settings:delivery-optimization                            |
|Update & Security |Find My Device                      |ms-settings:findmydevice                                     |
|Update & Security |For developers                      |ms-settings:developers                                       |
|Update & Security |Recovery                            |ms-settings:recovery                                         |
|Update & Security |Launch Security Key Enrollment      |ms-settings:signinoptions-launchsecuritykeyenrollment        |
|Update & Security |Troubleshoot                        |ms-settings:troubleshoot                                     |
|Update & Security |Windows Security                    |ms-settings:windowsdefender                                  |
|Update & Security |Windows Insider Program             |ms-settings:windowsinsider                                   |
|Update & Security |Windows Update                      |ms-settings:windowsupdate                                    |
|Update & Security |Windows Update-Active hours         |ms-settings:windowsupdate-activehours                        |
|Update & Security |Windows Update-Advanced options     |ms-settings:windowsupdate-options                            |
|Update & Security |Windows Update-Optional updates     |ms-settings:windowsupdate-optionalupdates                    |
|Update & Security |Windows Update-Restart options      |ms-settings:windowsupdate-restartoptions                     |
|Update & Security |Windows Update-Seeker on demand     |ms-settings:windowsupdate-seekerondemand                     |
|Update & Security |Windows Update-View update history  |ms-settings:windowsupdate-history                            |
|User Accounts     |Provisioning                        |ms-settings:workplace-provisioning                           |
|User Accounts     |Repair token                        |ms-settings:workplace-repairtoken                            |
|User Accounts     |Provisioning                        |ms-settings:provisioning                                     |
|User Accounts     |Windows Anywhere                    |ms-settings:windowsanywhere                                  |

## Resources

1. [Medium article explaining these in more detail](https://medium.com/@dbilanoski/how-to-use-windows-settings-app-with-ms-settings-uri-scheme-including-a-complete-list-of-them-52c19f83f3bd)
2. [CSV collection of Win11 URI commands](https://gist.githubusercontent.com/dbilanoski/8e931a0072b61ea215996a18fd215fb3/raw/f0ca6e81c241a72c76e65e7dbb7eb91612b0bfc2/windows11-ms-settings-uri-commands.csv)
