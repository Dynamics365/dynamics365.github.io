# Extend on-hand inventory entities FinOps


There are some entities that you can use to get the on-hand inventory from Dynamics 365 Finance Operations:

```cs
InventorySiteOnHandEntity
InventorySiteOnHandV2Entity
InventWarehouseInventoryStatusOnHandEntity
InventWarehouseInventoryStatusOnHandV2Entity
InventWarehouseOnHandEntity
InventWarehouseOnHandV2Entity
```

You can extend the ententies by [adding new fields to tables and entities](/2022-09-23-add-custom-fields-to-composite-entity/).

After you add fields to tables that are used by the `inventSiteOnHandView` view, you must sync the engine so that the extensions are correctly recognized.

1. Extend the `InventSiteOnHandView` view by adding the extension field.
2. Extend the `InventSiteOnHandAggregatedView` view with the extension fields.
3. Extend the `InventSiteOnHandAggregatedViewBuilder` viewBuilder class by modifying the `getExtensionFields()` method. In this way, you map old view fields to new view fields when viewBuilder synchronization is run.

For example, you've added the following four fields to the InventTable table through extension:
• Custom field 1
• Custom field 2
• Custom field 3
• Custom field 4

In the case, you must modify the `getExtensionFields()` method in the following way.
