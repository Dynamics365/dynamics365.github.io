# How to get FinOps Azure storage blob connection string


In Dynamics 365 for Finance and Operations Azure storage is used to 

* Store files for [Attachments](https://learn.microsoft.com/en-us/dynamics365/fin-ops-core/fin-ops/organization-administration/configure-document-management)
* [Data import and export jobs](https://learn.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/data-import-export-job)
*  When FinOps prints a report via [Document Reporting Services](https://learn.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/analytics/document-reporting-services?context=%2Fdynamics365%2Fcontext%2Fcommerce), SSRS Reporting Services generates an [EMF image](https://ax.docentric.com/printing-reports-with-custom-fonts-in-dynamics-365-for-finance-and-operations/) for each page of the generated document. FinOps then temporarily saves the generated EMF files to Azure Blob storage.
*  [Printing archive](https://learn.microsoft.com/en-us/dynamicsax-2012/appuser-itpro/print-a-report-from-the-archive)

And more!

If you are using tier 1 Cloud-Hosted environment, you can find the azure storage from the Azure resource group in Azure portal.

For tier 1, UAT tier 2, and PROD Tier 3, you can create a runnable class and execute to get the Azure storage connection string.

```c#
using Microsoft.Dynamics.Clx.ServicesWrapper;
class GetAzureBlob
{
    public static void main(Args _args)
    {
        info('AzureStorageConnectionString = ' + CloudInfrastructure::GetCsuStorageConnectionString());
        info('ClientCertificateThumbprint = ' + CloudInfrastructure::GetCsuClientCertificateThumbprint());
    }
}
```

Once you have the connection string, you can connect it to [MS Azure Storage Explorer](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows), Power Automate, or consuming in any framwork/language for integration/backup purposes.

