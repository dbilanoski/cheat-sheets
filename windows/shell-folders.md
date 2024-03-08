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