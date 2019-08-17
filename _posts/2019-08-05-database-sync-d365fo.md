---
date: 2019-08-05 00:00:00+00:00
title: Dynamics 365 for finance and operations database synchronization using command line 
tags:
- database synchronization
- Power shell
key: 2019-08-05-database-sync-d365fo
cover: /assets/images/test2.jpg
header:
    theme: dark
    background: 'linear-gradient(135deg, rgb(34, 139, 87), rgb(139, 34, 139))'
article_header:
    type: overlay
    theme: dark
    background_color: '#203028'
    background_image:
        gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
        src: /docs/assets/images/cover3.jpg
---

You can either execute the script for *cloud-hosted*, *onebox VHD*, or *UAT* (Azure DB) environment.
Open PowerShell, run following script

```K:\AOSService\webroot\bin\Microsoft.Dynamics.AX.Deployment.Setup.exe -bindir "K:\AosService\PackagesLocalDirectory" metadatadir "K:\AosService\PackagesLocalDirectory" -sqluser "axdbadmin" -sqlserver "." -sqldatabase "AxDB" -setupmode "sync" -syncmode "fullall" -isazuresql "false" -sqlpwd "************"```

AX DB user you can get from LCS, if you want to sync Azure DB please change **-isazuresql** to True
