# Environment Variables In Windows

Environment variables store data that's used by the operating system and other programs.

* They are always stored as strings.
* Unlike in Linux or macOS, they are not case sensitive here.

To list them all, use these commands:
* In PowerShell: `dir env:`
* In CMD: `set`
## Configure Environment Variables

Those can be temporary (process scoped) or persistent spanning machine wide or only for the current user.

When you change environment variables in PowerShell, the change affects only the current session making the variable temporary (like `set` command in CMD ore `setenv` command in UNIX-based systems does). For a permanent Machine or User scoped variables, you must use the methods of the **System.Environment** class.

### Set Temporary Session Environment Variable

These are gone when you close the terminal session in which are created.

**Syntax:** `$Env:<variable-name>`

**Example**
```powershell
$Env:PSQL_USERNAME = 'postgres'
```

### Set Permanent Environment Variable

The **System.Environment** class provides the `GetEnvironmentVariable()` and `SetEnvironmentVariable()` methods to get and modify environment variables.

**Syntax**
`[Environemnt]::SetEnvironmentVariable('variable name', 'variable value', 'scope')`

**Examples**

```powershell
# Create permantent user scope variable
[Environemnt]::SetEnvironmentVariable('PSQL_USERNAME', 'postgres')

# Create permantent machine scope variable
[Environemnt]::SetEnvironmentVariable('PSQL_USERNAME', 'postgres', 'machine')
```


### Control Panel GUI Approach

To make a persistent change to an environment variable on Windows using the System Control Panel:

1. Open the System Control Panel.
2. Select **System**.
3. Select **Advanced System Settings**.
4. Go to the **Advanced** tab.
5. Select **Environment Variables...**.
6. Make your changes.

## Resources

1. [Microsoft Docs On Environment Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7.4)
