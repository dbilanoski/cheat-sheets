# Creating Shortcuts With PowerShell

PowerShell can create symbolic links, but not shortcuts natively.

```powershell
# Symbolic Link Cration
New-Item -ItemType SymbolicLink -Path "C:\example-link.lnk" -Target "C:\Users\dbilanoski\Documents\some-text.txt"
```

## Symbolic Links And Shortcuts

[Symbolic links](https://en.wikipedia.org/wiki/Symbolic_link) are direct file system links (interpreted by the file system) while shortcuts are regular files with paths interpreted by the Windows UI.

When needing a Windows shortcut, choose it over a symlink for several reasons:

1. Shortcuts track referenced files, reducing the likelihood of breaking.
2. They can open network locations, configuration panels, and shell folders.
3. Shortcuts can start in a specific folder context.
4. Symlinks cannot include command-line arguments or be configured with icons.

## Creating Shortcuts

To create shortcuts using PowerShell, you'll need a  [COM](https://learn.microsoft.com/en-us/windows/win32/com/component-object-model--com--portal)object to instantiate the [WScript.Shell](https://learn.microsoft.com/en-us/previous-versions//aew9yb99(v=vs.85)) class, which provides the necessary methods for this task.

### Basic Example

```powershell
$ShortcutTarget= "path-to-your-target\name-of-your-target"  
$ShortcutFile = "path-to-your-to-be-shortcut\name-of-the-shortcut.lnk"  
$WScriptShell = New-Object -ComObject WScript.Shell  
$Shortcut = $WScriptShell.CreateShortcut($ShortcutFile)  
$Shortcut.TargetPath = $ShortcutTarget  
$Shortcut.IconLocation = "path-to-your-icon\icon-name.ico"  
$Shortcut.Save()
```

### One-liners

```powershell
# For PowerShell scripts
$ws = New-Object -ComObject WScript.Shell; $s = $ws.CreateShortcut('path-to-your-to-be-shortcut\name-of-the-shortcut.lnk'); $s.TargetPath = 'path-to-your-target\name-of-your-target'; $s.IconLocation = 'path-to-your-icon\icon-name.ico'; $s.Save()  
  
# For batch (CMD) scripts  
powershell -ExecutionPolicy Bypass -Command "$ws = New-Object -ComObject WScript.Shell; $s = $ws.CreateShortcut('path-to-your-to-be-shortcut\name-of-the-shortcut.lnk'); $s.TargetPath = 'path-to-your-target\name-of-your-target'; $s.IconLocation = 'path-to-your-icon\icon-name.ico'; $s.Save()"
```

### Specific Examples
How to configure target paths and arguments when accessing different target types.

```powershell
# Targeting a file  
$Shortcut.TargetPath = "C:\Windows\System32\notepad.exe"  
  
# Targeting a folder  
$Shortcut.TargetPath = "C:\Users\Public\Documents"  
  
# Targeting a network share  
$Shortcut.TargetPath = "C:\Windows\explorer.exe"  
$Shortcut.Arguments = "\\192.168.10.2\shares"  
  
# Targeting with environment variables  
$Shortcut.TargetPath = $env:userprofile + "\Downloads"  
  
# Targeting a .cpl file  
$Shortcut.TargetPath = "C:\Windows\System32\mmsys.cpl"  
  
# Targeting by using MS:SETTINGS URI scheme  
$Shortcut.TargetPath = "ms-settings:installed-apps"  
  
# Targeting with shell command by name  
$Shortcut.TargetPath = "C:\Windows\explorer.exe"  
$Shortcut.Arguments = "shell:NetworkPlacesFolder"  
  
# Targeting with shell command by CLSID GUID  
$Shortcut.TargetPath = "C:\Windows\explorer.exe"  
$Shortcut.Arguments = "shell:::{BB06C0E4-D293-4f75-8A90-CB05B6477EEE}"  
  
# Targeting by traversing a dll with rundll32  
$Shortcut.TargetPath = "C:\Windows\System32\rundll32.exe"  
$Shortcut.Arguments = "shell32.dll,Control_RunDLL inetcpl.cpl,,4"
```


## References

1. [My Medium.com article about shortcuts creation with PowerShell with examples and intricacies around selection icons from DLLs](https://medium.com/@dbilanoski/how-to-tuesdays-shortcuts-with-powershell-how-to-make-customize-and-point-them-to-places-1ee528af2763)
2. [“Shell: Folder Shortcuts” article at ss64.com](https://ss64.com/nt/shell.html)
3. [“List of Windows 11 CLSID Key (GUID) Shortcuts” article at elevenforum.com](https://www.elevenforum.com/t/list-of-windows-11-clsid-key-guid-shortcuts.1075/)