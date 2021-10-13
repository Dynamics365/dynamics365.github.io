# Microsoft AX 2012 Test Data Transfer Tool


## SUMMARY

Tool can be used for two different purposes. One is to install **demo data** and other one is elaborated as below. Customers often need to have a production dataset to use when building and validating customizations in **non-production** environments.

`The Microsoft Dynamics AX 2012 Test Data Transfer Tool (beta)` is a tool that helps move data between from production to non-production environments or from non-production environments to production environments to make a new production environment. But you must be careful becasue the tool imports data table by table and deletes the data in the table before importing. Hence it is highly advised against running the tool for import in production environments.

## BENEFITS

* Export and import data outside AX, without running an AOS instance.

* Export and import processing are faster compared to other tools because this tool is based on SQL Server bcp.

* The tool can work around the table/field metadata changes between builds and environments and hence can be used to move data from build to build, and environment to environment even when there are customizations, and metadata changes.

* The tool minimally changes data during the import process. The only data the tool changes are the entity IDs (IDs related to table, field, etc.) that are stored as data and that could change with each deployment. The tool recognizes the changes and patches the data with the AXIDs of the system that the data is being imported into.

* The data file format is the standard format produced by SQL Server bcp. The output is text based and can be stored and compared against other versions in a version control system.

### How to

1. Download **AX2012TestDataTransferTool.zip** file from [LCS](https://lcs.dynamics.com/)

![Image of Microsoft AX 2012 Test Data Transfer Tool](/imagesposts/Microsoft_Dyanmics_AX_2012_Test_Data_Transfer_Tool.png "ahihi")

**_PROD Environment_**

2. Run the setup file in SQL SERVER environment and complete the installation.

3. *"C:\Program Files (x86)\Microsoft Dynamics AX 2012 Test Data Transfer Tool (Beta)"* file appears automatically. Find the `MetadataXMLGenerator.xpo` file and import it into AX.

4. A job named `MetadataXMLGenrator` appears among `AOT/jobs`. Find and run the job.

5. Job generates a file named `MetaData.Xml` and gives you a file path via infolog.

6. Copy `MetaData.Xml` file and paste in *"C:\Program Files (x86)\Microsoft Dynamics AX 2012 Test Data Transfer Tool (Beta)\[Lists]"* file in Prod(Golden) SQL SERVER. Overwrite the existing `MetaData.Xml` file.

**_DEV or TEST Environment_**

7. Repeat the steps 2-6 for DEV environment.

8. The windows user who is going to execute the process should have access MicrosoftDynamicsAx ve Model database in DEV and Prod SQL servers. "Read" is enough for (Prod) exporting. "Full" right is enough for importing (DEV).

9. The windows user who is going to execute the process should have "full" access *"C:\Program Files (x86)\Microsoft Dynamics AX 2012 Test Data Transfer Tool (Beta)"* in both DEV and Prod SQL servers. System generates logs here.

10. Now, Live (Golden) environment's data will be exported. Prepare a file to export live(Golden) data on Live SQL Server. For instance `C:\DC_EXPORT`

11. Type the following command in command line and initiate the exportation process:
`DP.exe EXPORT Directory>> Database name>> Server>>`  
`DP.exe EXPORT C:\DC_EXPORT "MicrosoftDynamicsAX" "ServerName"`

12. Copy the exported file `(C:\DC_EXPORT)` to DEV environment

13. Stop DEV AOS service

14. Type the following command in command line and initiate the importation process:  
`DP.exe IMPORT Directory>> Database name>> Server>>`

15. Start DEV AOS service.

*Note*: DP.exe EXPORT/IMPORT commands must be run from the related directories
