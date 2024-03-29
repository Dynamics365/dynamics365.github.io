# How to install two instances SSRS on one server


## Scenario:

I have 2 AOS AX (maybe same in one server or different servers), but only one server for reporting server (I will install and configure multiple SRS instances in this server). Thing is how can we install and configure 2 SSRS instances on same server and running for 2 AOS.

![two_instances_SSRS_on_one_server_architechture](two_instances_SSRS_on_one_server_architechture.jpg "Two SSRS instance are running same server")

## Problems:

First you need to install 2 SSRS instance in same server, and install `reporting service component` for first AOS, this step is very simple. The problems come when we install second `reporting service component` for second AOS.

Reporting service component uses **business connector** to connect to AX, and it's saved on configuration in Registry. When we install second `reporting service component` it will through message that component already installed.

## How to do:

* After installed second instance into `C:\Program Files\Microsoft SQL Server\MSRS12.InstanceName\Reporting Services\ReportServer\bin`, we need to create one `Dynamics.AX.ReportConfiguration.axc` file by **AX configuration client** with second AOS information.

![how-to-install-2-instances-ssrs-on-1-server-2](how-to-install-2-instances-ssrs-on-1-server-2.jpg)

* Open `MS Dyanmics AX Management Shell`, run command below:

`Install-AXReportInstanceExtensions –ReportServerInstanceName [SSRSInstanceName] -Credential [DomainNameUserName]`

![how-to-install-2-instances-ssrs-on-1-server-3](how-to-install-2-instances-ssrs-on-1-server-3.jpg)


* Go to `C:\Program Files\Microsoft SQL Server\MSRS12.SecondInstance\Reporting Services\ReportServer` fix `fileconfi` with value from `Execution` to `FullTrust`

![how-to-install-2-instances-ssrs-on-1-server-4](how-to-install-2-instances-ssrs-on-1-server-4.jpg "how-to-install-2-instances-ssrs-on-1-server-4")

After this, remember restart reporting services.

![how-to-install-2-instances-ssrs-on-1-server-6](how-to-install-2-instances-ssrs-on-1-server-6.png "how-to-install-2-instances-ssrs-on-1-server-6")

From now on, you can run 2 AX reporting instance in same server.
