# Windows Shell Locations

Shell folders in Windows provide a unified way for users and applications to access various data types, integral to the graphical user interface (GUI). Unlike regular directories, they often act as virtual folders, combining data from different locations for intuitive management. 

Users can customize these folders, and developers leverage APIs for programmatic access, enhancing software integration and functionality.

## How To Access Them

From the RUN dialog or from Explorer by appending them to the shell command as a name by using `shell:shell-name` syntax of by the GUID by using `shell:::{GUID}` syntax.

```powershell
# By Name
shell:Cookies

# By the CLSID GUID
shell :::{559D40A3-A036-40FA-AF61-84CB430A4D34}
```

To access them from command line interfaces such as cmd \ PowerShell or make [[shortcuts-with-poweshell | Windows shortcuts]] to them, shell commands needs to be passed to the explorer.exe as an argument.

```powershell
# By Name
explorer "shell:Cookies"

# By the CLSID GUID
explorer "shell :::{559D40A3-A036-40FA-AF61-84CB430A4D34}"
```

## Finding And Listing Them In Registry

They can be found in several places in the Registry. While some of them might not be Shell Folders perse, perspective of this listing is to see which one are accessible programmatically in the previously explained manner.

1. By looking at properties of: `[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders]`
   
2. By looking at the names of the *FolderDescription* in: `[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions`

```powershell
# List them with PowerShell
$Folders = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions"
Get-ChildItem $Folders | select PSPath | Get-ItemProperty | select Name
```

3. By looking at [Class ID keys](https://learn.microsoft.com/en-us/windows/win32/com/clsid-key-hklm) which have `ShellFolder` sub-key with associated [GUID](https://learn.microsoft.com/en-us/dotnet/api/system.guid?view=net-8.0) in: `HKEY_CLASSES_ROOT\CLSID\{GUID}`

```powershell
# List them wih powershell
Get-ChildItem -Path 'Registry::HKEY_CLASSES_ROOT\CLSID' | Where-Object {$_.getsubkeynames() -contains "ShellFolder"} | select PSChildName, {(get-itemproperty $_.pspath).'(default)'}
```

Often, there will be subkeys in those items which are also accessible. 

```powershell
# Fantastic script from Keith Miller at "https://superuser.com/questions/1528616/how-to-get-list-of-all-shell-locations-in-windows" adjusted to export list to csv

$Shell = New-Object -ComObject shell.application
$DT = $Shell.Namespace(0)
$Output = "C:\clsid-shell-folders.csv"

Function Unfold ($oFolder) {
$oFolder.Items() | ?{ ($_.IsFolder -eq $True) -and ($_.Name -notLike 'Fonts') } | ForEach{
UnFold $_.GetFolder
}
$_.GetFolder.Items() | select Name, Path
}

$DT.Items() | ?{($_.IsFolder -eq $True) -and
        ($_.Name -match 'Control Panel')} | % {
            Unfold $_.GetFolder
} | select name, path -unique | Sort Path | Export-CSV -Path $Output

```


## Complete Lists Of Windows 11 Shell Locations

### Shell Folders By Name

| **Name**                    | **Shell Command Shortcut**                   |
|-----------------------------|----------------------------------------------|
| 3D Objects                  | explorer "shell:3D Objects"                  |
| AccountPictures             | explorer "shell:AccountPictures"             |
| AddNewProgramsFolder        | explorer "shell:AddNewProgramsFolder"        |
| Administrative Tools        | explorer "shell:Administrative Tools"        |
| AppData                     | explorer "shell:AppData"                     |
| AppDataDesktop              | explorer "shell:AppDataDesktop"              |
| AppDataDocuments            | explorer "shell:AppDataDocuments"            |
| AppDataFavorites            | explorer "shell:AppDataFavorites"            |
| AppDataProgramData          | explorer "shell:AppDataProgramData"          |
| Application Shortcuts       | explorer "shell:Application Shortcuts"       |
| AppMods                     | explorer "shell:AppMods"                     |
| AppsFolder                  | explorer "shell:AppsFolder"                  |
| AppUpdatesFolder            | explorer "shell:AppUpdatesFolder"            |
| Cache                       | explorer "shell:Cache"                       |
| Camera Roll                 | explorer "shell:Camera Roll"                 |
| CameraRollLibrary           | explorer "shell:CameraRollLibrary"           |
| Captures                    | explorer "shell:Captures"                    |
| CD Burning                  | explorer "shell:CD Burning"                  |
| ChangeRemoveProgramsFolder  | explorer "shell:ChangeRemoveProgramsFolder"  |
| Common Administrative Tools | explorer "shell:Common Administrative Tools" |
| Common AppData              | explorer "shell:Common AppData"              |
| Common Desktop              | explorer "shell:Common Desktop"              |
| Common Documents            | explorer "shell:Common Documents"            |
| Common Programs             | explorer "shell:Common Programs"             |
| Common Start Menu           | explorer "shell:Common Start Menu"           |
| Common Start Menu Places    | explorer "shell:Common Start Menu Places"    |
| Common Startup              | explorer "shell:Common Startup"              |
| Common Templates            | explorer "shell:Common Templates"            |
| CommonDownloads             | explorer "shell:CommonDownloads"             |
| CommonMusic                 | explorer "shell:CommonMusic"                 |
| CommonPictures              | explorer "shell:CommonPictures"              |
| CommonRingtones             | explorer "shell:CommonRingtones"             |
| CommonVideo                 | explorer "shell:CommonVideo"                 |
| ConflictFolder              | explorer "shell:ConflictFolder"              |
| ConnectionsFolder           | explorer "shell:ConnectionsFolder"           |
| Contacts                    | explorer "shell:Contacts"                    |
| ControlPanelFolder          | explorer "shell:ControlPanelFolder"          |
| Cookies                     | explorer "shell:Cookies"                     |
| CredentialManager           | explorer "shell:CredentialManager"           |
| CryptoKeys                  | explorer "shell:CryptoKeys"                  |
| CSCFolder                   | explorer "shell:CSCFolder"                   |
| Desktop                     | explorer "shell:Desktop"                     |
| Development Files           | explorer "shell:Development Files"           |
| Device Metadata Store       | explorer "shell:Device Metadata Store"       |
| DocumentsLibrary            | explorer "shell:DocumentsLibrary"            |
| Downloads                   | explorer "shell:Downloads"                   |
| DpapiKeys                   | explorer "shell:DpapiKeys"                   |
| Favorites                   | explorer "shell:Favorites"                   |
| Fonts                       | explorer "shell:Fonts"                       |
| GameTasks                   | explorer "shell:GameTasks"                   |
| History                     | explorer "shell:History"                     |
| ImplicitAppShortcuts        | explorer "shell:ImplicitAppShortcuts"        |
| InternetFolder              | explorer "shell:InternetFolder"              |
| Libraries                   | explorer "shell:Libraries"                   |
| Links                       | explorer "shell:Links"                       |
| Local AppData               | explorer "shell:Local AppData"               |
| Local Documents             | explorer "shell:Local Documents"             |
| Local Downloads             | explorer "shell:Local Downloads"             |
| Local Music                 | explorer "shell:Local Music"                 |
| Local Pictures              | explorer "shell:Local Pictures"              |
| Local Videos                | explorer "shell:Local Videos"                |
| LocalAppDataLow             | explorer "shell:LocalAppDataLow"             |
| LocalizedResourcesDir       | explorer "shell:LocalizedResourcesDir"       |
| MAPIFolder                  | explorer "shell:MAPIFolder"                  |
| MusicLibrary                | explorer "shell:MusicLibrary"                |
| My Music                    | explorer "shell:My Music"                    |
| My Pictures                 | explorer "shell:My Pictures"                 |
| My Video                    | explorer "shell:My Video"                    |
| MyComputerFolder            | explorer "shell:MyComputerFolder"            |
| NetHood                     | explorer "shell:NetHood"                     |
| NetworkPlacesFolder         | explorer "shell:NetworkPlacesFolder"         |
| OEM Links                   | explorer "shell:OEM Links"                   |
| OneDrive                    | explorer "shell:OneDrive"                    |
| OneDriveCameraRoll          | explorer "shell:OneDriveCameraRoll"          |
| OneDriveDocuments           | explorer "shell:OneDriveDocuments"           |
| OneDriveMusic               | explorer "shell:OneDriveMusic"               |
| OneDrivePictures            | explorer "shell:OneDrivePictures"            |
| Original Images             | explorer "shell:Original Images"             |
| Personal                    | explorer "shell:Personal"                    |
| PhotoAlbums                 | explorer "shell:PhotoAlbums"                 |
| PicturesLibrary             | explorer "shell:PicturesLibrary"             |
| Playlists                   | explorer "shell:Playlists"                   |
| PrintersFolder              | explorer "shell:PrintersFolder"              |
| PrintHood                   | explorer "shell:PrintHood"                   |
| Profile                     | explorer "shell:Profile"                     |
| ProgramFiles                | explorer "shell:ProgramFiles"                |
| ProgramFilesCommon          | explorer "shell:ProgramFilesCommon"          |
| ProgramFilesCommonX64       | explorer "shell:ProgramFilesCommonX64"       |
| ProgramFilesCommonX86       | explorer "shell:ProgramFilesCommonX86"       |
| ProgramFilesX64             | explorer "shell:ProgramFilesX64"             |
| ProgramFilesX86             | explorer "shell:ProgramFilesX86"             |
| Programs                    | explorer "shell:Programs"                    |
| Public                      | explorer "shell:Public"                      |
| PublicAccountPictures       | explorer "shell:PublicAccountPictures"       |
| PublicGameTasks             | explorer "shell:PublicGameTasks"             |
| PublicLibraries             | explorer "shell:PublicLibraries"             |
| Quick Launch                | explorer "shell:Quick Launch"                |
| Recent                      | explorer "shell:Recent"                      |
| Recorded Calls              | explorer "shell:Recorded Calls"              |
| RecordedTVLibrary           | explorer "shell:RecordedTVLibrary"           |
| RecycleBinFolder            | explorer "shell:RecycleBinFolder"            |
| ResourceDir                 | explorer "shell:ResourceDir"                 |
| Retail Demo                 | explorer "shell:Retail Demo"                 |
| Ringtones                   | explorer "shell:Ringtones"                   |
| Roamed Tile Images          | explorer "shell:Roamed Tile Images"          |
| Roaming Tiles               | explorer "shell:Roaming Tiles"               |
| SavedGames                  | explorer "shell:SavedGames"                  |
| SavedPictures               | explorer "shell:SavedPictures"               |
| SavedPicturesLibrary        | explorer "shell:SavedPicturesLibrary"        |
| Screenshots                 | explorer "shell:Screenshots"                 |
| Searches                    | explorer "shell:Searches"                    |
| SearchHistoryFolder         | explorer "shell:SearchHistoryFolder"         |
| SearchHomeFolder            | explorer "shell:SearchHomeFolder"            |
| SearchTemplatesFolder       | explorer "shell:SearchTemplatesFolder"       |
| SendTo                      | explorer "shell:SendTo"                      |
| Start Menu                  | explorer "shell:Start Menu"                  |
| Startup                     | explorer "shell:Startup"                     |
| SyncCenterFolder            | explorer "shell:SyncCenterFolder"            |
| SyncResultsFolder           | explorer "shell:SyncResultsFolder"           |
| SyncSetupFolder             | explorer "shell:SyncSetupFolder"             |
| System                      | explorer "shell:System"                      |
| SystemCertificates          | explorer "shell:SystemCertificates"          |
| SystemX86                   | explorer "shell:SystemX86"                   |
| Templates                   | explorer "shell:Templates"                   |
| ThisDeviceFolder            | explorer "shell:ThisDeviceFolder"            |
| ThisPCDesktopFolder         | explorer "shell:ThisPCDesktopFolder"         |
| User Pinned                 | explorer "shell:User Pinned"                 |
| UserProfiles                | explorer "shell:UserProfiles"                |
| UserProgramFiles            | explorer "shell:UserProgramFiles"            |
| UserProgramFilesCommon      | explorer "shell:UserProgramFilesCommon"      |
| UsersFilesFolder            | explorer "shell:UsersFilesFolder"            |
| UsersLibrariesFolder        | explorer "shell:UsersLibrariesFolder"        |
| VideosLibrary               | explorer "shell:VideosLibrary"               |
| Windows                     | explorer "shell:Windows"                     |

### Shell Folders By CLSID GUID

| Name                                                                                       | Shell Command Shortcut                                                                                                                                                                         |
|--------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| %UserProfile%                                                                              | explorer "shell:::{59031a47-3f72-44a7-89c5-5595fe6b30ee}"                                                                                                                                      |
| %UserProfile%\Desktop                                                                      | explorer "shell:::{B4BFCC3A-DB2C-424C-B029-7FE99A87C641}"                                                                                                                                      |
| %UserProfile%\Documents                                                                    | explorer "shell:::{A8CDFF1C-4878-43be-B5FD-F8091C1C60D0}"                                                                                                                                      |
| %UserProfile%\Downloads                                                                    | explorer "shell:::{088e3905-0323-4b02-9826-5d99428e115f}"                                                                                                                                      |
| %UserProfile%\Pictures                                                                     | explorer "shell:::{24ad3ad4-a569-4530-98e1-ab02f9417aa8}"                                                                                                                                      |
| Add Network Location                                                                       | explorer "shell:::{D4480A50-BA28-11d1-8E75-00C04FA31A86}"                                                                                                                                      |
| Additional Information                                                                     | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\resultPage"                                                                                                                           |
| Advanced Problem Reporting Settings                                                        | explorer "shell:::{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}\pageAdvSettings"                                                                                                                      |
| Advanced sharing settings                                                                  | explorer "shell:::{8E908FC9-BECC-40f6-915B-F4CA0E70D03D}\Advanced"                                                                                                                             |
| All Control Panel Items                                                                    | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0"                                                                                                                                    |
| Applications                                                                               | explorer "shell:::{4234d49b-0245-4df3-b780-3893943456e1}"                                                                                                                                      |
| AutoPlay                                                                                   | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{9C60DE1E-E5FC-40F4-A487-460851A8D915}"                                                                                           |
| Backup and Restore (Windows 7)                                                             | explorer "shell:::{B98A2BEA-7D42-4558-8BD1-832F41BAC6FD}"                                                                                                                                      |
| Bluetooth Devices                                                                          | explorer "shell:::{28803F59-3A75-4058-995F-4EE5503B023C}"                                                                                                                                      |
| Change Security and Maintenance settings                                                   | explorer "shell:::{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}\Settings"                                                                                                                             |
| Change Settings                                                                            | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\settingPage"                                                                                                                          |
| Change Your Name                                                                           | explorer "shell:::{60632754-c523-4b62-b45c-4172da012619}\pageRenameMyAccount"                                                                                                                  |
| Clock and Region                                                                           | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\6"                                                                                                                                    |
| Color and Appearance                                                                       | explorer "shell:::{ED834ED6-4B5A-4bfe-8F11-A626DCB6A921}\pageColorization"                                                                                                                     |
| Color Management                                                                           | explorer "shell:::{B2C761C6-29BC-4f19-9251-E6195265BAF1}"                                                                                                                                      |
| Command Folder                                                                             | explorer "shell:::{437ff9c0-a07f-4fa0-af80-84b6c6440a16}"                                                                                                                                      |
| Common Places FS Folder                                                                    | explorer "shell:::{d34a6ca6-62c2-4c34-8a7c-14709c1ad938}"                                                                                                                                      |
| Connection Properties                                                                      | explorer "shell:::{241D7C96-F8BF-4F85-B01F-E2B043341A4B}\PropertiesPage"                                                                                                                       |
| Control Panel                                                                              | explorer "shell:::{5399E694-6CE5-4D6C-8FCE-1D8870FDCBA0}"                                                                                                                                      |
| Control Panel (category view)                                                              | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}"                                                                                                                                      |
| Control Panel (icons view)                                                                 | explorer "shell:::{21EC2020-3AEA-1069-A2DD-08002B30309D}"                                                                                                                                      |
| Control Panel All Tasks                                                                    | explorer "shell:::{ED7BA470-8E54-465E-825C-99712043E01C}"                                                                                                                                      |
| Create a power plan                                                                        | explorer "shell:::{025A5937-A6BE-4686-A844-36FE4BEC8B6D}\pageCreateNewPlan"                                                                                                                    |
| Credential Manager                                                                         | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{1206F5F1-0569-412C-8FEC-3204630DFB70}"                                                                                           |
| Customize Settings                                                                         | explorer "shell:::{4026492F-2F69-46B8-B9BF-5654FC07E423}\PageConfigureSettings"                                                                                                                |
| Default Programs                                                                           | explorer "shell:::{2559a1f7-21d7-11d4-bdaf-00c04f60b9f0}"                                                                                                                                      |
| Desktop Background                                                                         | explorer "shell:::{ED834ED6-4B5A-4bfe-8F11-A626DCB6A921}\pageWallpaper"                                                                                                                        |
| Device Manager                                                                             | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{74246BFC-4C96-11D0-ABEF-0020AF6B0B7A}"                                                                                           |
| Ease of Access - Center                                                                    | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}"                                                                                                                                      |
| Ease of Access Centre - Cognition: Get recommendations to make your computer easier to use | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageQuestionsCognitive"                                                                                                               |
| Ease of Access Centre - Make it easier to focus on tasks                                   | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageEasierToReadAndWrite"                                                                                                             |
| Ease of Access Centre - Make the computer easier to see                                    | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageEasierToSee"                                                                                                                      |
| Ease of Access Centre - Make the keyboard easier to use                                    | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageKeyboardEasierToUse"                                                                                                              |
| Ease of Access Centre - Make the mouse easier to use                                       | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageEasierToClick"                                                                                                                    |
| Ease of Access Centre - Use text or visual alternatives for sounds                         | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageEasierWithSounds"                                                                                                                 |
| Ease of Access Centre - Use the computer without a display                                 | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageNoVisual"                                                                                                                         |
| Ease of Access Centre - Use the computer without a mouse or keyboard                       | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageNoMouseOrKeyboard"                                                                                                                |
| Ease of Access Centre - Vision: Get recommendations to make your computer easier to use    | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageQuestionsEyesight"                                                                                                                |
| Ease of Access                                                                             | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\7"                                                                                                                                    |
| Email (default app)                                                                        | explorer "shell:::{2559a1f5-21d7-11d4-bdaf-00c04f60b9f0}"                                                                                                                                      |
| Favourites                                                                                 | explorer "shell:::{323CA680-C24D-4099-B94D-446DD2D7249E}"                                                                                                                                      |
| File Explorer Options                                                                      | explorer "shell:::{6DFD7C5C-2451-11d3-A299-00C04F8EF6AF}"                                                                                                                                      |
| Font settings                                                                              | explorer "shell:::{93412589-74D4-4E4E-AD0E-E0CB621440FD}"                                                                                                                                      |
| Frequent folders                                                                           | explorer "shell:::{3936E9E4-D92C-4EEE-A85A-BC16D5EA0819}"                                                                                                                                      |
| Gallery                                                                                    | explorer "shell:::{E88865EA-0E1C-4E20-9AA6-EDCD0212C87C}"                                                                                                                                      |
| Get Programs                                                                               | explorer "shell:::{15eae92e-f17a-4431-9f28-805e482dafd4}"                                                                                                                                      |
| Hardware and Sound                                                                         | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\2"                                                                                                                                    |
| History                                                                                    | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\historyPage"                                                                                                                          |
| Home                                                                                       | explorer "shell:::{679f85cb-0220-4080-b29b-5540cc05aab6}"                                                                                                                                      |
| Hyper-V Remote File Browser                                                                | explorer "shell:::{0907616E-F5E6-48D8-9D61-A91C3D28106D}"                                                                                                                                      |
| Indexing Options                                                                           | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{87D66A43-7B11-4A28-9811-C86EE395ACF7}"                                                                                           |
| Installed Updates                                                                          | explorer "shell:::{d450a8a1-9568-45c7-9c0e-b4f9fb4537bd}"                                                                                                                                      |
| Internet Properties                                                                        | explorer "shell:::{A3DD4F92-658A-410F-84FD-6FBBBEF2FFFE}"                                                                                                                                      |
| Keyboard                                                                                   | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{725BE8F7-668E-4C7B-8F90-46BDB0936430}"                                                                                           |
| Keyboard Properties                                                                        | explorer "shell:::{725BE8F7-668E-4C7B-8F90-46BDB0936430}"                                                                                                                                      |
| Libraries                                                                                  | explorer "shell:::{031E4825-7B94-4dc3-B131-E946B44C8DD5}"                                                                                                                                      |
| Linux                                                                                      | explorer "shell:::{B2B4A4D1-2754-4140-A2EB-9A76D9D7CDC6}"                                                                                                                                      |
| Location Information (Phone and Modem Control Panel)                                       | explorer "shell:::{40419485-C444-4567-851A-2DD7BFA1684D}"                                                                                                                                      |
| Manage Accounts                                                                            | explorer "shell:::{60632754-c523-4b62-b45c-4172da012619}}\pageAdminTasks"                                                                                                                      |
| Media Servers                                                                              | explorer "shell:::{289AF617-1CC3-42A6-926C-E6A863F0E3BA}"                                                                                                                                      |
| Media streaming options                                                                    | explorer "shell:::{8E908FC9-BECC-40f6-915B-F4CA0E70D03D}\ShareMedia"                                                                                                                           |
| Microsoft Print to PDF                                                                     | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{A8A91A66-3A7D-4424-8D24-04E180695C7A}\Provider%5CMicrosoft.Base.DevQueryObjects//DDO:%7BB02567CB-B5D8-11EE-8D8C-74D83E9479BB%7D" |
| Mouse Properties                                                                           | explorer "shell:::{6C8EEC18-8D75-41B2-A177-8831D59D2D50}"                                                                                                                                      |
| Music folder                                                                               | explorer "shell:::{1CF1260C-4DD0-4ebb-811F-33C572699FDE}"                                                                                                                                      |
| My Documents                                                                               | explorer "shell:::{450D8FBA-AD25-11D0-98A8-0800361B1103}"                                                                                                                                      |
| Network (Places)                                                                           | explorer "shell:::{F02C1A0D-BE21-4350-88B0-7367FC96EF3C}"                                                                                                                                      |
| Network and Internet                                                                       | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\3"                                                                                                                                    |
| Network and Sharing Centre                                                                 | explorer "shell:::{8E908FC9-BECC-40f6-915B-F4CA0E70D03D}"                                                                                                                                      |
| Network Connections                                                                        | explorer "shell:::{7007ACC7-3202-11D1-AAD2-00805FC1270E}"                                                                                                                                      |
| Notification Area Icons                                                                    | explorer "shell:::{05d7b0f4-2121-4eff-bf6b-ed3f69b894d9}"                                                                                                                                      |
| Offline Files Folder                                                                       | explorer "shell:::{AFDB1F70-2A4C-11d2-9039-00C04F8EEB3E}"                                                                                                                                      |
| OneDrive                                                                                   | explorer "shell:::{018D5C66-4533-4307-9B53-224DE2ED1FE6}"                                                                                                                                      |
| Pen and Touch                                                                              | explorer "shell:::{F82DF8F7-8B9F-442E-A48C-818EA735FF9B}"                                                                                                                                      |
| Personalization                                                                            | explorer "shell:::{ED834ED6-4B5A-4bfe-8F11-A626DCB6A921}"                                                                                                                                      |
| Phone and Modem                                                                            | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{40419485-C444-4567-851A-2DD7BFA1684D}"                                                                                           |
| Phone and Modem Control Panel (Location Information)                                       | explorer "shell:::{40419485-C444-4567-851A-2DD7BFA1684D}"                                                                                                                                      |
| Portable Devices                                                                           | explorer "shell:::{35786D3C-B075-49b9-88DD-029876E11C01}"                                                                                                                                      |
| Power Options - Edit Plan Settings                                                         | explorer "shell:::{025A5937-A6BE-4686-A844-36FE4BEC8B6D}\pagePlanSettings"                                                                                                                     |
| Printers                                                                                   | explorer "shell:::{2227A280-3AEA-1069-A2DE-08002B30309D}"                                                                                                                                      |
| Problem Details                                                                            | explorer "shell:::{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}\pageReportDetails"                                                                                                                    |
| Problem Reporting Settings                                                                 | explorer "shell:::{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}\pageSettings"                                                                                                                         |
| Problem Reports                                                                            | explorer "shell:::{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}\pageProblems"                                                                                                                         |
| Programs                                                                                   | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\8"                                                                                                                                    |
| Public user root folder                                                                    | explorer "shell:::{4336a54d-038b-4685-ab02-99bb52d3fb8b}"                                                                                                                                      |
| Quick access                                                                               | explorer "shell:::{679f85cb-0220-4080-b29b-5540cc05aab6}"                                                                                                                                      |
| Recent folders                                                                             | explorer "shell:::{22877a6d-37a1-461a-91b0-dbda5aaebc99}"                                                                                                                                      |
| Recent Items Instance Folder                                                               | explorer "shell:::{4564b25e-30cd-4787-82ba-39e73a750b14}"                                                                                                                                      |
| Recovery                                                                                   | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{9FE63AFD-59CF-4419-9775-ABCC3849F861}"                                                                                           |
| Recycle Bin                                                                                | explorer "shell:::{645FF040-5081-101B-9F08-00AA002F954E}"                                                                                                                                      |
| Reliability Monitor                                                                        | explorer "shell:::{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}\pageReliabilityView"                                                                                                                  |
| Remote Assistance                                                                          | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\raPage"                                                                                                                               |
| Remote File Browser for Hyper-V                                                            | explorer "shell:::{0907616E-F5E6-48D8-9D61-A91C3D28106D}"                                                                                                                                      |
| Removable Drives                                                                           | explorer "shell:::{F5FB2C77-0E2F-4A16-A381-3E560C68BC83}"                                                                                                                                      |
| Removable Storage Devices                                                                  | explorer "shell:::{a6482830-08eb-41e2-84c1-73920c2badb9}"                                                                                                                                      |
| Restore defaults                                                                           | explorer "shell:::{4026492F-2F69-46B8-B9BF-5654FC07E423}\PageRestoreDefaults"                                                                                                                  |
| Results Folder                                                                             | explorer "shell:::{2965e715-eb66-4719-b53f-1672673bbefa}"                                                                                                                                      |
| Run                                                                                        | explorer "shell:::{2559a1f3-21d7-11d4-bdaf-00c04f60b9f0}"                                                                                                                                      |
| Search                                                                                     | explorer "shell:::{2559a1f8-21d7-11d4-bdaf-00c04f60b9f0}"                                                                                                                                      |
| Search Results                                                                             | explorer "shell:::{9343812e-1c37-4a49-a12e-4b2d810d956b}"                                                                                                                                      |
| Search Troubleshooting                                                                     | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\searchPage"                                                                                                                           |
| Set up Filter Keys                                                                         | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageFilterKeysSettings"                                                                                                               |
| Set up Mouse Keys                                                                          | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageMouseKeysSettings"                                                                                                                |
| Set up Repeat and Slow Keys                                                                | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageRepeatRateSlowKeysSettings"                                                                                                       |
| Set up Sticky Keys                                                                         | explorer "shell:::{D555645E-D4F8-4c29-A827-D93C859C4F2A}\pageStickyKeysSettings"                                                                                                               |
| Show Desktop                                                                               | explorer "shell:::{3080F90D-D7AD-11D9-BD98-0000947B0257}"                                                                                                                                      |
| Speech Properties                                                                          | explorer "shell:::{D17D1D6D-CC3F-4815-8FE3-607E7D5D10B3}"                                                                                                                                      |
| Sync Centre                                                                                | explorer "shell:::{9C73F5E5-7AE7-4E32-A8E8-8D23B85255BF}"                                                                                                                                      |
| Sync Setup                                                                                 | explorer "shell:::{2E9E59C0-B437-4981-A647-9C34B9B90891}"                                                                                                                                      |
| System - About                                                                             | explorer "shell:::{BB06C0E4-D293-4f75-8A90-CB05B6477EEE}"                                                                                                                                      |
| System and Security                                                                        | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\5"                                                                                                                                    |
| System Settings                                                                            | explorer "shell:::{025A5937-A6BE-4686-A844-36FE4BEC8B6D}\pageGlobalSettings"                                                                                                                   |
| Tablet PC Settings                                                                         | explorer "shell:::{80F3F1D5-FECA-45F3-BC32-752C152E456E}"                                                                                                                                      |
| Task View                                                                                  | explorer "shell:::{3080F90E-D7AD-11D9-BD98-0000947B0257}"                                                                                                                                      |
| Taskbar and Navigation                                                                     | explorer "shell:::{0DF44EAA-FF21-4412-828E-260A8728E7F1}"                                                                                                                                      |
| This Device                                                                                | explorer "shell:::{5b934b42-522b-4c34-bbfe-37a3ef7b9c90}"                                                                                                                                      |
| This PC                                                                                    | explorer "shell:::{20D04FE0-3AEA-1069-A2D8-08002B30309D}"                                                                                                                                      |
| Troubleshoot problems - Hardware and Sound                                                 | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\devices"                                                                                                                              |
| Troubleshoot problems - Network and Internet                                               | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\network"                                                                                                                              |
| Troubleshoot problems - Programs                                                           | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\applications"                                                                                                                         |
| Troubleshoot problems - System and Security                                                | explorer "shell:::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}\system"                                                                                                                               |
| Troubleshooting                                                                            | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{C58C4893-3BE0-4B45-ABB5-A63E4B8C8651}"                                                                                           |
| User Accounts                                                                              | explorer "shell:::{7A9D77BD-5403-11d2-8785-2E0420524153}"                                                                                                                                      |
| User Accounts (Control Panel)                                                              | explorer "shell:::{60632754-c523-4b62-b45c-4172da012619}"                                                                                                                                      |
| User Accounts (netplwiz)                                                                   | explorer "shell:::{7A9D77BD-5403-11d2-8785-2E0420524153}"                                                                                                                                      |
| User Pinned                                                                                | explorer "shell:::{1f3427c8-5c10-4210-aa03-2ee45287d668}"                                                                                                                                      |
| Videos folder                                                                              | explorer "shell:::{A0953C92-50DC-43bf-BE83-3742FED03C9C}"                                                                                                                                      |
| Windows Defender Firewall                                                                  | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{4026492F-2F69-46B8-B9BF-5654FC07E423}"                                                                                           |
| Windows Defender Firewall - Allowed Apps                                                   | explorer "shell:::{4026492F-2F69-46B8-B9BF-5654FC07E423}\pageConfigureApps"                                                                                                                    |
| Windows Features                                                                           | explorer "shell:::{67718415-c450-4f3c-bf8a-b487642dc39b}"                                                                                                                                      |
| Windows Mobility Centre                                                                    | explorer "shell:::{5ea4f148-308c-46d7-98a9-49041b1dd468}"                                                                                                                                      |
| Windows Search                                                                             | explorer "shell:::{2559a1f8-21d7-11d4-bdaf-00c04f60b9f0}"                                                                                                                                      |
| Windows Tools                                                                              | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{D20EA4E1-3957-11D2-A40B-0C5020524153}"                                                                                           |
| Work Folders                                                                               | explorer "shell:::{26EE0668-A00A-44D7-9371-BEB064C98683}\0\::{ECDB0924-4208-451E-8EE0-373C0956DE16}"                                                                                           |
