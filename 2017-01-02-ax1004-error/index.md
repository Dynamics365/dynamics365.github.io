# AX1004 error in Dynamics AX


I got this error at version Dynamics 2012 R3 CU9, SQL Server 2014, Windows Server 2012 R2.

Those kind of errors will come when you move report from Dev Server to Live Server, even compilation in *DP Class, Query, Contract, table* … without any error

![Image](/imagesposts/AX1004-error.png "Image_rounded"){:.rounded}

<span style="color: red">AX1004: Reference System.Core, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089 was not a valid model assembly.</span>

<span style="color: red">MSB3644: The reference assemblies for framework “.NETFramework,Version=v4.0” were not found. To resolve this, install the SDK or Targeting Pack for this framework version or retarget your application to a version of the framework for which you have the SDK </span>

### Cause

There is no reference Assemblies Version 4.0  under `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework` on Windows Server 2012 R2 (only Version 4.5 it have)

### Solution

Copy those Assemblies from your any where (Windows 7,8,10) to Server

Restore and compile again.

Thank you for reading!

