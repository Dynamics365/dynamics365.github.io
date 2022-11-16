# Data Entity Tips


- [Custom from staging to target](#custom-from-staging-to-target)
- [Exporting large amounts of data](#exporting-large-amounts-of-data)
- [Handling errors messages](#handling-errors-messages)
  - [Creating logs in *View excecution log*](#creating-logs-in-view-excecution-log)
  - [Creating logs in *View staging data*](#creating-logs-in-view-staging-data)
- [Entity methods](#entity-methods)
  - [mapEntityToDataSource](#mapentitytodatasource)

#### Custom from staging to target

When importing data into AX using data entities, sometimes there is no way to match data structure between data source (xml file, excel spredsheet, etc) and AX table. For instance:
  * Single line from a spredsheel source needs to be split amoung table header and table line in D365.
  * Records creation is assisted by some class and cannot be directly created by DMF (Data Management Framework).

From your data entity, create a new static field following the below template:
> Kindly note this is a personal quick recommendation. Obviously this code can be improved.

```csharp
public static container copyCustomStagingToTarget(DMFDefinitionGroupExecution _dmfDefinitionGroupExecution)
{
    CustCustomerStaging staging;
    CustCustomerStaging stagingUpd;
    
    // Iterate through all records with have not been processed
    while select forupdate staging
        where staging.ExecutionId    == _dmfDefinitionGroupExecution.ExecutionId
        &&   (staging.TransferStatus == DMFTransferStatus::NotStarted || staging.TransferStatus == DMFTransferStatus::Validated)
    {
        try
        {
            ttsbegin;
            // Do your stuff

            staging.TransferStatus = DMFTransferStatus::Completed;
            staging.update();
            ttscommit;
        }
        catch (Exception::Error)
        {
            error("Something wrong has happened.");
        }
    }    

    ttsbegin;
    update_recordset staging
        setting TransferStatus = DMFTransferStatus::Error
        where staging.DefinitionGroup == _dmfDefinitionGroupExecution.DefinitionGroup
        &&    staging.ExecutionId     == _dmfDefinitionGroupExecution.ExecutionId
        &&   (staging.TransferStatus == DMFTransferStatus::NotStarted || staging.TransferStatus == DMFTransferStatus::Validated);
    ttscommit;

    // Method returns a container containing the quantities of inserted and updated records.
    select count(RecId) from staging
        where staging.DefinitionGroup == _dmfDefinitionGroupExecution.DefinitionGroup
            && staging.ExecutionId == _dmfDefinitionGroupExecution.ExecutionId
            && staging.TransferStatus == DMFTransferStatus::Completed;

    return [staging.RecId, 0];
}
```

In order to *copyCustomStagingToTarget* be executed, you need to set field *Set-based processing* as **TRUE**.  
*Data management workspace > Data entities button*  
![set-based](https://github.com/anderson-joyle/D365O-Cheatsheet/blob/master/prints/set_base_field.PNG)

#### Exporting large amounts of data

A list of few things to take into consideration before exporting a millions of records:
* Do your best to skip staging. This wont be possible if your entity has any computed field or container field. Data management will info about any other field type that is preventing your to skip staging.
* When exporting to a BYOD, make sure you have enough [DTU](https://docs.microsoft.com/en-gb/azure/azure-sql/database/service-tiers-dtu) assigned to your target Azure database.

#### Handling errors messages

Basically there are two types of data entities errors messages: from *View excecution log* message and *View staging data* message. *View excecution log* displays messages in macro way e.g. "Could not connect into system X", while *View staging data* displays messages to each distinct staging table record.

##### Creating logs in *View excecution log*

Any message printed during DMF execcution (info, warning and error) will end up being displayed in *View excecution log* area.

##### Creating logs in *View staging data*

To display custom log message to specifics staging records, use the following snippet:  

```csharp
DMFStagingValidationLog::insertLogs(_dmfDefinitionGroupExecution.DefinitionGroup,
                                    _dmfDefinitionGroupExecution.ExecutionId,
                                    DMFEntity::find(_dmfDefinitionGroupExecution.Entity),
                                    staging.RecId,
                                    "",
                                    "My custom log message",
                                    DMFSourceTarget::Target);
```

#### Entity methods

##### mapEntityToDataSource

* **Direction**: Importing  
* **Purpose**: When importing, use it to fill either datasource or entity fields based on entity fields.  
* **Example**: In *CustCustomerEntity.mapEntityToDataSource()*, *EmployeeResponsibleNumber* field value is used to retrive worker record id and set it into *MainContactWorker* field from entity itself.
