---
date: 2020-04-06 00:00:00+00:00
title: Getting onebox VHD Dynamics 365 finance and operations virtual machine 
tags:
- onebox vhd
- Dynamics 365 finance and operations
- upgrade
- LCS 
- OneBox
author: Max Nguyen
key: Getting-onebox-VHD-Dynamics-365-finance-and-operations-virtual-machine
cover: /assets/images/test2.jpg
---

## Download Dynamics 365 finance and operations VHD files

* Go to the LCS main page and select **Shared asset library** or go to Shared Asset Library.

* Select the asset type **Downloadable VHD**.

* Find the VHD you are looking for based on the desired Finance and Operation version. The VHD is divided into multiple file parts that you need to download. For example, the asset files that start with "VHD - 10.0.5" are the different files you need in order to install version 10.0.5.

* Download all files (parts) associated with the desired VHD to a local folder.

* After the download is complete, run the executable file that you downloaded, accept the software license agreement, and choose a file path to extract the VHD to.

* This creates a local VHD file that you can use to run a local virtual machine.

* Sign in to the VM by using the following credentials:

  * User name: **Administrator**
  * Password: **pass@word1**

* Provision the administrator user.

## Rename VM

* Rename and restart the machine before you start development or connect to Azure DevOps.
* Update the server name in SQL Server

  * To be able to login, Start SQL Server with **administrator** or using the user **axdbadmin** has password **AOSWebSite@12**
  * Run following query

    ```sql
    sp_dropserver [old_name]
    sp_addserver [new_name], local
    ```

  * Restart SQL service

* Open Reporting Services Configuration Manager for SQL Server 2016, then Select Database, select Change Database, and use the new server name.
* Update the Azure Storage Emulator

    a. From the Start menu, open Microsoft Azure Storage Emulator - v4.0, and run the following commands.

    ```AzureStorageEmulator.exe start```

    > If you got an error **_Port conflict with existing application_**, please check this [post](https://nuxulu.com/2020/04/05/Azure-Storage-Emulator-Port-conflict-with-existing-application.html).

    b. This command verifies that the emulator is running.

    ```AzureStorageEmulator.exe status```

    c. Update the server name

    ```AzureStorageEmulator.exe init -server new_name```

    For more information about Azure storage emulator please follow <https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator>

## Location of packages, source code, and other AOS configurations

On a VM, you can find most of the application configuration by opening the web.config file of AOSWebApplication.

1. Start IIS.

2. Go to _Sites > AOSWebApplication_.

3. Right-click, and then click **Explore** to open File Explorer.

4. Open the **web.config** file in Notepad or another text editor. The following keys are of interest to many developers and administrators:

    * **Aos.MetadataDirectory** – This key points to the location of the packages folder that contains platform and application binaries, and also source code. (Source code is available only in development environments.) Typical values are: _c:\packages, c:\AosServicePackagesLocalDirectory_, and _J:AosServicePackagesLocalDirectory_.

    * **DataAccess.Database** – This key holds the name of the database.

    * **Aos.AppRoot** – This key points to the root folder of the Application Object Server (AOS) web application.

## Redeploying or restarting the runtime on the VM

To restart the local runtime and redeploy all the packages, follow these steps.

1. Open File Explorer, and go to _C:\CustomerServiceUnit_.

2. Right-click **AOSDeploy.cmd**, and then click **Run as administrator**.

This process might take a while. The process is completed when the cmd.exe window closes. If you just want to restart AOS (without redeploying the runtime), run iisreset from an administrator Command Prompt window, or restart AOSWebApplication from IIS.

## Upgrade to the latest

Please check this document
<https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/deployment/install-deployable-package>

That's it, thank you for reading.
