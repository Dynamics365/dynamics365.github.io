---
date: 2021-01-17 00:00:00+00:00
title: Deploy SSRS reports in Dynamics 365 Finance, SCM using Powershell
tags:
- SSRS
- Dynamics 365 Finance
- Dynamics 365 Supply Chain Management
- Deploy
author: Max Nguyen
key: Deploy-SSRS-reports-in-Dynamics-365-Finance,-SCM-using-Powershell
cover: /assets/imagesposts/How-to-upgrade-to-Visual-Studio-2017-3.png
---

Open Windows PowerShell in Admin mode 

Navigate to the PowerShell scripts

```powershell
cd K:\AosService\PackagesLocalDirectory\Plugins\AxReportVmRoleStartupTask\
```

Execute the below commands:

* For deploying all SSRS reports

```powershell
./DeployAllReportsToSSRS.ps1 -PackageInstallLocation “K:\AosService\PackagesLocalDirectory”
```

* For deploying the specific reports

```powershell
./DeployAllReportsToSSRS.ps1 -Module ApplicationSuite -ReportName <ReportName> -PackageInstallLocation “K:\AosService\PackagesLocalDirectory”
```

**Example:**

```powershell
./DeployAllReportsToSsrs.ps1 -Module MaxCustomization -ReportName MaxCheque_US.Report -PackageInstallLocation "K:\AosService\PackagesLocalDirectory"
```

```powershell
./DeployAllReportsToSsrs.ps1 -Module ApplicatoinSuite -ReportName Cust* -PackageInstallLocation "C:\AosService\PackagesLocalDirectory"
```


