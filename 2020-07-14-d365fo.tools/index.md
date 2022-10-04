# How To d365fo.tools


## Basic

{{< admonition info Reference >}}
* https://github.com/d365collaborative/d365fo.tools/wiki
{{< /admonition >}}

```powershell
#Installation
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Install-Module -Name d365fo.tools #-AllowClobber
Import-Module -Name d365fo.tools
Uninstall-Module -Name d365fo.tools
Update-Module -Name d365fo.tools 

#help
Get-Command -Module d365fo.tools
Get-Help New-D365Bacpac
Get-Help New-D365Bacpac -Full
Get-Help New-D365Bacpac -Examples 
Get-Help Get-D365AzureDevOpsNuget  -Full

#deploy report
Publish-D365SsrsReport -Module Kaynecustomization -ReportName KACheque_US.Report

#environment
Get-D365Environment -All
Stop-D365Environment -All
Stop-D365Environment -Aos -Batch
Start-D365Environment -All
Restart-D365Environment -All

#users
Import-D365ExternalUser -Id "mnguyen" -Name "Max Nguyen" -Email "max@automaly.com" -Company "002"
Get-D365Module
Get-Help Set-D365Admin -Full
Get-Help Set-D365SysAdmin -Full
Set-D365Admin "max@automaly.com"
Get-D365User -Email "max@automaly.com" | Enable-D365User
```

## LCS

```powershell
Get-D365LcsApiToken -ClientId "***" -Username "" -Password "" -LcsApiUri "https://lcsapi.lcs.dynamics.com" | Set-D365LcsApiConfig -ProjectId 000000

Invoke-D365LcsEnvironmentStart -ProjectId 1269585 -EnvironmentId "d5dc45bb-"
Invoke-D365LcsEnvironmentStop -ProjectId 1269585 -EnvironmentId "d5dc45bb-" 
```

## Import a bacpac file into a Tier1 environment

```powershell
Invoke-D365InstallSqlPackage
Import-D365Bacpac -BacpacFile "J:\MSSQL_BACKUP\AxDB_UAT.bacpac" -ImportModeTier1 -NewDatabaseName AxDB_UAT -ShowOriginalProgress
Stop-D365Environment -All
Switch-D365ActiveDatabase -NewDatabaseName "AxDB_UAT"
Invoke-D365DBSync -ShowOriginalProgress
Start-D365Environment -All
```

## DB Sync & Build

```powershell
# Get database backup
Get-D365LcsDatabaseBackups

 # Build
Invoke-D365DbSyncModule -Module "Kaynecustomization" -ShowOriginalProgress
Invoke-D365ModuleCompile -Module "Kaynecustomization" -ShowOriginalProgress
Invoke-D365DBSync -ShowOriginalProgress
```

## SSRS

```powershell
Publish-D365SsrsReport -Module GR -ReportName GR_InvSalesVelocityRpt.AutoDesign1
```
