# SSRS Report Server Settings Validation Error- AX 2012 R3 CU9


Recently I updated my dynamics Ax development Environment by restoring Database from Production Dynamics AX DB and I got this problem 

**Make sure that SQL Server Reporting Services is configured correctly. Verify the Web Service URL and Report Manager URL configuration in the SQL Reporting Services Configuration Manager.**

Anyway, The reporting Services are still working fine and i have already granted the AX Admin as System Administrator under site settings, Home folder settings and DynamicsAX folder with `“Browser, Content Manager, DynamicsAXBrowser, My Reports, Publisher, Report Builder”` roles.


Solution is we need disable UAC (I'm using Windows Server 2012 R2) by *Regedit*


go to Regedit: `“HKEY_LOCAL_MACHINESOFTWAREMicrosoftWindowsCurrentVersionpoliciessystem”` and changing the DWORD `“EnableLUA”` from 1 to 0.


After the reboot, UAC is disabled.

Happy AXsing
