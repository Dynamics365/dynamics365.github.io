# Develop custom Data Entities for Dual-write


[Dual-write](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/dual-write-home-page?WT.mc_id=BA-MVP-5003976) has been around for almost two years now. It’s one of the ways of **integrating Dynamics 365 Finance and Operations and [Dataverse](https://docs.microsoft.com/en-us/powerapps/maker/data-platform?WT.mc_id=BA-MVP-5003976)** along with [Virtual Entities](https://ariste.info/en/2020/09/dynamics-365-odata-cds-virtual-entities/).

The standard solution comes with many **out-of-the-box entities** available to synchronize. This has been one of the great improvements since Dual-write was made available in preview, when [Juanan](https://jatomas.com/) and I [demoed it in the 2019 Dynamics Saturday in Madrid](https://www.youtube.com/watch?v=z_dLGDF7Ci0).


![Dual write](https://static.ariste.info/wp-content/uploads/2021/03/DW-1024x1024.png "Develop custom Data Entities for Dual-write 1")

This is how Dual write really works

But what if we need to develop a **new custom Data Entity in MSDyn365FO** and use it in Dual-write? It’s easy but there’s some things we need to remember when doing it.

[](https://ariste.info/en/2021/03/develop-custom-data-entities-dual-write/#dual-write)Dual-write
------------------------------------------------------------------------------------------------

[Dual-write](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/dual-write-home-page?WT.mc_id=BA-MVP-5003976) is a bidirectional integration that will synchronously write in Dataverse when data is created, updated or deleted in MSDyn365FO in near-real-time. On the Finance and Operations side it uses data entities to export data to Dataverse.

Right now there’s a set of several OOB entities that come ready to be synchronized, and thanks to [Initial Sync](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/initial-sync-guidance?WT.mc_id=BA-MVP-5003976) we can populate data in Dataverse choosing FnO as the source when starting the sync, or also choose Dataverse as the source.

If you want to learn more about Dual-write you can:

*   Read [the docs](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/dual-write-home-page?WT.mc_id=BA-MVP-5003976) which have plenty of information. **Read the docs**. Always.
*   [Guidance for Dual-Write setup](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/connection-setup?WT.mc_id=BA-MVP-5003976)
*   [System requirements and prerequisites](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/requirements-and-prerequisites?WT.mc_id=BA-MVP-5003976)
*   Watch some of [Faisal Fareed](https://daxture.blogspot.com/)‘s sessions about Dual-write: [DynamicsCon](https://dynamicscon.com/) 2020: [The Power of Dual-write](https://www.youtube.com/watch?v=DbyLLQswM5o) or Scottish Summit 2021: [D365 FO integration with Dataverse – Dual write, Virtual Entities, OR Data Integrator](https://www.youtube.com/watch?v=O2iuM6dWklI). He’s got some more which you can find on Youtube.

[](https://ariste.info/en/2021/03/develop-custom-data-entities-dual-write/#create-the-data-entity)Create the Data-entity
------------------------------------------------------------------------------------------------------------------------

In Visual Studio we need to create the entity from our table. I’ve created a new table called AASBookTable with just four fields: BookId, Author, Name and ISBN. Its primary key is the BookId field which is also its **alternate key** and will be used as natural key in the entity.

Next, we create the data entity and make sure we’re marking the “**Enable data management capabilities**” checkbox:


![Dynamics 365 Finance and Operations Data entity wizard](https://static.ariste.info/wp-content/uploads/2021/03/DW1.png "Develop custom Data Entities for Dual-write 2")

Enable data management capabilities must be checked

If the entity doesn’t have data management enabled it won’t be displayed in the list in the Dual-write tables setup.

[](https://ariste.info/en/2021/03/develop-custom-data-entities-dual-write/#create-a-table-in-dataverse)Create a table in Dataverse
----------------------------------------------------------------------------------------------------------------------------------

Now we need to create a table in our Dataverse environment. This table must have at least some of the fields we want to synchronize to Dataverse **AND** a company field. **The [company](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/company-data?WT.mc_id=BA-MVP-5003976) concept doesn’t exist in Dataverse/CRM** but thanks to the OOB mappings and [Initial Sync](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/initial-sync-guidance?WT.mc_id=BA-MVP-5003976) we can solve this with just a few clicks and will have a company table in Dataverse with all our FnO legal entities.

![Company field related to table company](https://static.ariste.info/wp-content/uploads/2021/03/DW2.png "Develop custom Data Entities for Dual-write 3")

Company field related to table company

Following my example I’ve created a table with the same four fields and a company field with the **data type Lookup** and its related table Company, where the FnO legal entities are synchronized.

As I said, if we don’t create this field we won-t be able to setup Dual-write for this table.

[](https://ariste.info/en/2021/03/develop-custom-data-entities-dual-write/#create-table-map)Create table map
------------------------------------------------------------------------------------------------------------

Our table and data entity are ready, and now **we need to [create a mapping](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/enable-entity-map?WT.mc_id=BA-MVP-5003976) between them** in the Dual-write workspace in FnO. Click the “Add table map” button:

![Dynamics 365 Dual-write add table map](https://static.ariste.info/wp-content/uploads/2021/03/DW3.png "Develop custom Data Entities for Dual-write 4")

Dynamics 365 Dual-write add table map

A new dialog will open and we need to select the FnO entity and the Dataverse table:

![Entity map](https://static.ariste.info/wp-content/uploads/2021/03/DW4-edited.png "Develop custom Data Entities for Dual-write 5")

Entity map

Select the entity and table we’ve created and click save. Then we can define the field mapping:

![Dynamics 365 Dual-write field maps](https://static.ariste.info/wp-content/uploads/2021/03/DW13-1024x629.png "Develop custom Data Entities for Dual-write 6")

Dynamics 365 Dual-write field maps

Because I’ve created both it’s clear what to map. And after doing this we can click save and it’s done, right? No! **WRONG**! If we do just this we’ll get an error, this error:

> Project validation failed. SourceEntitySchema: Books has a primaryCompanyField set to DataAreaId and DestinationEntitySchema: cr008\_bookses doesn’t have primary company field set. Dual-write only supports mapping between cross-company entities or company-specific entities from both sides..keys are missing for either AX or CRM.keys are missing for either CRM or AX

Or we can also get an error regarding a missing integration key for the company field. In the end both are caused because **we’ve missed defining the integration key** for this new Dataverse table:

[](https://ariste.info/en/2021/03/develop-custom-data-entities-dual-write/#integration-key)Integration key
----------------------------------------------------------------------------------------------------------

Go back to the main Dual-write form and click on the “Integration key” button:

![Dual/write integration key](https://static.ariste.info/wp-content/uploads/2021/03/DW7.png "Develop custom Data Entities for Dual-write 7")

Dual-write integration key

The integration key will be **the same as the FnO data entity key, plus the company** if your data entity has a company context. Remember that when we create indexes in FnO the DataAreaId field isn’t included in the field, but it is in the SQL Server index along the partition field.

The integration key for our custom Dual-write mapping will look like this:

![Dual/write integration key](https://static.ariste.info/wp-content/uploads/2021/03/DW11.png "Develop custom Data Entities for Dual-write 8")

Dual/write integration key

Remember we’ve added the company field to our Dataverse table? You can see in the image above that the field includes the relation to the Company table in Dataverse. We won’t be able to save the field mapping if we create the key using our Dataverse table’s company field instead of its Company table relation, like this:

![Dual/write integration key](https://static.ariste.info/wp-content/uploads/2021/03/DW9.png "Develop custom Data Entities for Dual-write 9")

Dual/write integration key

See the difference? In the first image the field reads _c008\_company.cdm\_companycode_ while in the second one it’s _c008\_company_. If we set the integration key using the field in our table instead of the related table and save the fields’ mapping we’ll get an error saying the company is missing in the key because it’s expecting the relation!

[](https://ariste.info/en/2021/03/develop-custom-data-entities-dual-write/#action)Action!
-----------------------------------------------------------------------------------------

The table and field mappings are ready, just click run and go create a new book in the FnO form:

![Finance and Operations form](https://static.ariste.info/wp-content/uploads/2021/03/DW14.png "Develop custom Data Entities for Dual-write 10")

Finance and Operations form

Then we go to our Dataverse table and check its data…

![Dataverse table data](https://static.ariste.info/wp-content/uploads/2021/03/DW15.png "Develop custom Data Entities for Dual-write 11")

Dataverse table data

It’s there! And of course it’s working in both directions. If I create a record in Dataverse it’ll be created in FnO too. I’ll use the Excel add-in to add a new book:

![Dataverse Excel add-in](https://static.ariste.info/wp-content/uploads/2021/03/DW16.png "Develop custom Data Entities for Dual-write 12")

Dataverse Excel add-in

And after refreshing the form in FnO we can see it there too:

![Develop custom Data Entities for Dual-write 1](https://static.ariste.info/wp-content/uploads/2021/03/DW17.png "Develop custom Data Entities for Dual-write 13")

This is a really simple example of how we can create a custom table, use it in a data entity and then use this entity in our dual-write setup. It’s something that can be easily done but we need to **remember the “company thing”**, otherwise this will never work!

Dual-write is even easier to configure nowadays thanks to [LCS allowing us to create and link a new Dataverse environment when we deploy a new Finance and Operations environment](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/lcs-setup?WT.mc_id=BA-MVP-5003976).
