# Windows Server Update Services

## RSAT WSUS Dependencies
Running update reports from RSAT WSUS administration console will require installation of ReportViewer application which will also, depending on the server build, have other SQL related dependencies.

### Windows Server 2012 Edition

1. Install Microsoft SQLSysCLRTypes
   Go here: https://www.microsoft.com/en-us/download/details.aspx?id=56041
   Click Downloads > Select "SQLSysClrTypes.msi" (larger one) and install it

2. Install ReportViewer
   Go here: https://www.microsoft.com/en-us/download/details.aspx?id=35747
   Download and install ReportViewer.msi

3. Restart Windows Server Update Service console