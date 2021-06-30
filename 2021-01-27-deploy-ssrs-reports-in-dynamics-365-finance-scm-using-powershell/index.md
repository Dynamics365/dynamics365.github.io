# Deploy SSRS reports in Dynamics 365 Finance, SCM using Powershell


Open Windows PowerShell in Admin mode 

<!--more-->

Navigate to the PowerShell scripts

```ps
cd K:\AosService\PackagesLocalDirectory\Plugins\AxReportVmRoleStartupTask\
```

Execute the below commands:

* For deploying all SSRS reports

```ps
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



