# How to verify active inventory dimensions on an item


If you want to list items come along with their Actvie Dimension, you can do it easily with this simple job

   
```csharp
InventTable     inventTable;
InventDimParm   inventDimParm;
;
inventTable   = InventTable::find('110924');
inventDimParm =  InventDimParm::activeDimFlag(InventDimGroupSetup::newInventTable(inventTable));
if(inventDimParm.InventSiteIdFlag)
{
	info("Site Actived");
}
```

