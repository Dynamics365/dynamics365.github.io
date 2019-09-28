---
date: 2019-08-05 00:00:00+00:00
title: Dynamics 365 for finance and operations database synchronization using command line 
tags:
- database synchronization
- Power shell
author: Max Nguyen
key: 2019-08-05-database-sync-d365fo
cover: /assets/images/test2.jpg
---

You can either execute the script for *cloud-hosted*, *onebox VHD*, or *UAT* (Azure DB) environment.
Open PowerShell, run following script

```K:\AOSService\webroot\bin\Microsoft.Dynamics.AX.Deployment.Setup.exe -bindir "K:\AosService\PackagesLocalDirectory" metadatadir "K:\AosService\PackagesLocalDirectory" -sqluser "axdbadmin" -sqlserver "." -sqldatabase "AxDB" -setupmode "sync" -syncmode "fullall" -isazuresql "false" -sqlpwd "************"```

AX DB user you can get from LCS, if you want to sync Azure DB please change **-isazuresql** to True

<iframe src="https://onedrive.live.com/embed?cid=7198ABAAEE532EE3&resid=7198ABAAEE532EE3%21337409&authkey=ABl8QaiHw7y4r7g&em=2" width="402" height="346" frameborder="0" scrolling="no"></iframe>