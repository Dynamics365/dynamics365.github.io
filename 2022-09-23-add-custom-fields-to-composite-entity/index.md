# Add new fields to data entity or composite entity Dynamics 365


Let's take a composite entity `SalesOrderV3Entity` as an example.

### Scenario

I will need to add new fields to Sales order header that can be used in the composite entity `SalesOrderV3Entity`.
the composite entity `SalesOrderV3Entity` uses a standard data entity `SalesOrderHeaderV2Entity` for sales order header; so, we need to extend `SalesOrderHeaderV2Entity`.

### The Steps

1. Add new fields to Main table `SalesTable`
2. Add new fields to the entity `SalesOrderHeaderV2Entity`
3. Add new fields to the staging table `SalesOrderHeaderV2Staging`
4. Build and sync.
5. Go to Workspaces -> Data Management -> Framework parameters -> Advanced entity configuration settings -> Refresh entity list.

    You can automate the `Refresh entity list` task [here](https://d365fostuff.wordpress.com/2021/03/31/d365fo-integrations-auto-refresh-your-data-entity-list/)
    
6. Go to Workspaces -> Data Management -> Data Entities, find **SALES ORDER HEADERS V2** and *Generate mapping* for it.
The new custom fields will be generated for the entity mapping.

