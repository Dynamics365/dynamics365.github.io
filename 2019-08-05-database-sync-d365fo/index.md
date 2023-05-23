# Dynamics 365 for finance and operations database synchronization using command line


You can either execute the script for *cloud-hosted*, *onebox VHD*, or *UAT* environment. This is not applied with **one-box Microsoft hosted** environment.

Open PowerShell, run following script

```powershell
K:\AOSService\webroot\bin\Microsoft.Dynamics.AX.Deployment.Setup.exe 
-bindir "K:\AosService\PackagesLocalDirectory" 
metadatadir "K:\AosService\PackagesLocalDirectory" 
-sqluser "axdbadmin" -sqlserver "." -sqldatabase "AxDB" 
-setupmode "sync" -syncmode "fullall" 
-isazuresql "false" -sqlpwd "************" 
-logfilename "C:\Temp\dbsync.log"
```

For example

```powershell
K:\AOSService\webroot\bin\Microsoft.Dynamics.AX.Deployment.Setup.exe 
-bindir "K:\AosService\PackagesLocalDirectory" 
metadatadir "K:\AosService\PackagesLocalDirectory" 
-sqluser "axdbadmin" -sqlserver "." -sqldatabase "AxDB" 
-setupmode "sync" -syncmode "fullall" 
-isazuresql "false" -sqlpwd "AOSWebSite@123" 
-logfilename "C:\Temp\dbsync.log"
```

AX DB user you can get from LCS, if you want to sync Azure DB please change ```-isazuresql``` to *True*

You can also use [d365fo.tools](https://github.com/d365collaborative/d365fo.tools/blob/development/docs/Invoke-D365DBSync.md) to trigger the DB sync.

Tier 1
```ps
Invoke-D365DBSync -Verbose
```

Tier 2
```powershell
Invoke-D365DbSync -DatabaseServer .database.windows.net  -DatabaseName db_d365opsprod -SqlUser JIT-6h8cg -SqlPwd NjT -LogPath "C:\Temp\d365fo.tools\dbsync.log"
```


