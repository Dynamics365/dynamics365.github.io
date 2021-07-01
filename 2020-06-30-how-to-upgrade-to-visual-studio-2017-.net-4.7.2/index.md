# How to upgrade to Visual Studio 2017, .NET 4.7.2 for PU36/10.0.12 and higher


For the upcoming Dynamics 365 Finance Updates, Visual Studio 2017 and .NET runtime 4.7.2 required for PU36/10.0.12 or higher; New VMs deployed with PU36/10.0.12 or higher will have Visual Studio 2017 as well as .NET runtime 4.7.2 already installed. 

For your dev/test/build Tier1 VMs, Microsoft recommends just redeploying a new VM, and you will be all set! 

If you cannot deploy new VMs, please follow below steps to update .NET runtime, VS 2017 before installing PU36/10.0.12 or higher, this will be applied for **cloud-hosted environment**

### 1. Download VS Professional 2017 15.9 [here](https://download.visualstudio.microsoft.com/download/pr/ac05c4f5-0da1-429f-8701-ce509ac69926/7b51a7b8db36f977745cf3e19ef1e9c62ebf29f704aebafce443626b779cb4f8/vs_Professional.exe) and install it

### 2. The .NET runtime download is available [here](https://dotnet.microsoft.com/download/dotnet-framework/net472) by clicking on the _Download .NET Framework 4.7.2 Runtime_ and running the installation, restart required.

### 3. Go to Dynamics Lifecycle Services and download PU36/10.0.12 or any higher package which is a part of the Platform and application binary, the VSIX file is located in the _DevToolsService\Scripts_ folder.

![Image](/imagesposts/How-to-upgrade-to-Visual-Studio-2017-1.png)

You need to ```install Microsoft.Dynamics.Framework.Tools.Installer``` and then ```Microsoft.Dynamics.Framework.Tools.InternalDevTools```.
After the installation, open the VS2017 if you should see **Dynamics 365** menu extension like below

![Image](/imagesposts/How-to-upgrade-to-Visual-Studio-2017-2.png)

### 4. If you try to access the client before applying the package you will get the error like below in event viewer and unable to access the environment, so you need to apply an update first.

AX is shutting down due to an error. Serialization version mismatch detect, make sure the runtime dlls are in sync with the deployed metadata. Version of file '194'. Version of dll '193'.
{:.error}

### 5. In LCS, navigate to your cloud-hosted environment, and apply Platform and application binary package PU36/10.0.12 or higher. After completed upgrading, you will be able to access the environment client. 

Overall, there is no change in the compiler, metadata; this is only an update to the Visual Studio extensions and NET runtime for the tier1 VMs.
