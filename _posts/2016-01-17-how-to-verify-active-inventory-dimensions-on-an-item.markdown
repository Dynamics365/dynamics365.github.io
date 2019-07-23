---
title: How to verify active inventory dimensions on an item

date: 2016-01-17 16:22:50.000000000 +07:00
author: Max Nguyen
categories: [ax2012]
tags: [inventdim]
key: d365
---

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
