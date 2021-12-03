# AX 2012 R3 CU8 CU9 and TFS Online 2013 Error


When you try connecting between TFS and Dynamics AX CU8, CU9 and you got error like this:

_Error 1_
Team Foundation services are not available from server  __****.visualstudio.com/defaultcollection.__

Technical information (for administrator):

 `TF400813: Resource not available for anonymous access. Client authentication required.`

_Error 2_

*Could not load file or assembly 'Microsoft.TeamFoundation.Client, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The system cannot find the file specified.*

Client computers that are not running Visual Studio 2010 must have the [Team Foundation Server 2010 SP1 Object Model](https://visualstudiogallery.msdn.microsoft.com/a37e19fb-3052-4fc9-bef7-4a4682069a75) installed to use TFS source control with Microsoft Dynamics AX.

TFS support TFS 2010, TFS 2012, TFS 2013 and TFS online, but to connect to these from AX you will need “TFS 2010 object model Sp1” which is client to connect to server.

After install Team Foundation Server 2010 SP1 Object Model above. Client computers that are not running Visual Studio 2010 must have hotfix KB [2662296](http://support.microsoft.com/kb/2662296) installed to use TFS source control with Microsoft Dynamics AX.
Once you have installed the above , restart your AOS and open AX client again, then try to check-in some Jobs.
