---
alias: gpresults, Get-GPResultantSetOfPolicy, RSOP
---

# Group Policy Data Retrieval and Reporting

To retrieve Group Policy information on managed computers in a sense which policy settings were applied, there are  two methods to get the information:

1.  [GPResult.exe](ttps://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-xp/bb490915(v=technet.10)) command-line utility
2. [Get-GPResultantSetOfPolicy](https://learn.microsoft.com/en-us/powershell/module/grouppolicy/get-gpresultantsetofpolicy?view=windowsserver2022-ps) PowerShell cmdlet

Both can be used to retrieve data remotely and format it a sharable reporting format, such as html.

## Notes on usage

* To get computer configuration details, both need to be executed elevated (CMD or Powershell "as administrator"). 
* If target user is not specified, it will pull data for the current user (owner of the execution). This means you get administrator user context if you execute it from elevated CMD/Powershell.
* If target user is specified, make sure that user was logged-in at least once on the target computer. Otherwise there will be no data.

## GPResult.exe

Displays Group Policy settings and Resultant Set of Policy (RSOP) for a user or a computer.

Syntax and argument options available [here](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-xp/bb490915(v=technet.10)).

### Examples

Basic summary data on local computer (applied user and computer objects, not details)

```cmd
gpresult /r
```

Basic summary data on remote computer showing only computer applied objects

```cmd
gpresult /s remoteComputerName /r /scope computer
```

Verbose details from remote computer with provided credentials of admin user with access rights targeting certain user and saved as a policy.txt text file locally

```cmd
gpresult /s remoteComputerName /u domain\adminUserName /p password /user targetusername /z > C:\policy.txt 
```

Verbose details from remote computer with provided credentials of admin user with access rigths targeting certain user and saved as a html file locally

```cmd
gpresult /s remoteComputerName /u domain\userName /p password /user targetusername /H C:\policy.html
```


## Get-GPResultantSetOfPolicy

Powershell [cmdlet](https://learn.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7.3) used to get and write the RSoP information for a user, a computer, or both to a file.

Syntax and parameter options available [here](https://learn.microsoft.com/en-us/powershell/module/grouppolicy/get-gpresultantsetofpolicy?view=windowsserver2022-ps)

### Examples

Generate a report for the default user that is running on the current session and save it as XML file

```powershell
Get-GPResultantSetOfPolicy -ReportType Xml -Path "c:\reports\LocalUserAndComputerReport.xml"
```


Generate a report for the specified remote computer and save it as XML file
* `-Computer` argument expects fully qualified domain name or domain name\\computer name

```powershell
Get-GPResultantSetOfPolicy -ReportType Xml -Path "c:\reports\LocalUserAndComputerReport.xml" -Computer remoteComputerName.domain.com
```


Generate a report for the specified remote computer and user and save it as HTML file

```powershell
Get-GPResultantSetOfPolicy -ReportType HTML -Path "c:\reports\LocalUserAndComputerReport.xml" -Computer remoteComputerName.domain.com -User domainName\userName
```

Generate a report for the specified remote computer and user in HTML format and save it to the specified file


## Scripted solution with IGPM interface

Here we are using Group Policy Management COM Object ([IGPM interface](https://learn.microsoft.com/en-us/windows/win32/api/gpmgmt/nn-gpmgmt-igpm)) which is the base for gpresult.exe and Get-ResultantSetOfPolicy and we can access it's methods and create more usable scripts for remote RSOP gathering.

Be sure to adjust machine name, user name and reporting file paths in those scripts.

```powershell
# Resources
# https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/exporting-resultant-set-of-policy-rsop-data-using-powershell/ba-p/1217934
# https://azurecloudai.blog/2015/09/24/powershell-retrieve-group-policy-details-for-remote-computer/

# Config (adjust these to your situation) 
$machineName = "odin\hrospcwpkg1"
$userName = "odin\ops60webid"


# Instantiate the GPMgmt.GPM class that allows access to most of the GPMC functionality  
$gpmObject = New-Object -ComObject GPMgmt.GPM

# Obtain all constants and save it in a variable
$gpmConstants = $gpmObject.GetConstants()

# Create a reference RSOP object using required constrants
$rsopObject = $gpmObject.GetRSOP($gpmConstants.RSOPModeLogging,$null,0)


# Configure RSOP object's target computer and user name to be used when querying for results
# Note: If we need the RSOP data for only Computer without considering User imposed Group Policy data, we need to use “RsopLoggingNoUser” constant value instead of $rsopObject.LoggingUser.
$rsopObject.LoggingComputer = $machineName
$rsopObject.LoggingUser = $userName

 
# Query the target computer for RSOP GPO data.
$rsopObject.CreateQueryResults()

 
# Export data to html report (adjust the file path if needed)
$rsopReport = $rsopObject.GenerateReportToFile($gpmConstants.ReportHTML , "C:\Temp\RSoP.html")

# Export data to xml report (adjust the file path if needed)
$rsopReport = $rsopObject.GenerateReportToFile($gpmConstants.ReportXML , "C:\Temp\RSoP.xml")

# Release the object
$rsopObject.ReleaseQueryResults()
```


## Resources

1. [gpresult official documentation](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-xp/bb490915(v=technet.10))
2. [Get-gpresultantsetofpolicy official documentation](https://learn.microsoft.com/en-us/powershell/module/grouppolicy/get-gpresultantsetofpolicy?view=windowsserver2022-ps)
3. [Good read on scripting this 1](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/exporting-resultant-set-of-policy-rsop-data-using-powershell/ba-p/1217934)
4. [Good read on scripting this 2](https://azurecloudai.blog/2015/09/24/powershell-retrieve-group-policy-details-for-remote-computer/)

