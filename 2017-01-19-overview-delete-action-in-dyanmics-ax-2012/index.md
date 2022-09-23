# Overview delete action in Dyanmics AX 2012


We normally create action delete in foreign table to make sure referential integrity in Logic and validation.

The delete action has the following options: **None, Restricted, Cascade, Cascade + Restricted**

<!--more-->

#### None:

This has no effect, and effectively disables the delete action. This is useful if you want to specifically state *do nothing* so that someone else doesn't try to correct what seems to be an omission.

#### Restricted:

This will prevent the record from being deleted if there are records in the related table that match the selected relation. This occurs within the `validateDelete` table event, which is called by the delete table event.

#### Cascade:

This will delete the record in the related table, based on the relation. Ex: A sales order line is of no use without a sales order. This is an extension of the delete table event.

#### Cascade + Restricted

This is a little special. In a two-table scenario, it is the same as *Restricted*. It will stop the record from being deleted if a related record exists. But if the record is being deleted as part of a cascade from a table related to it, the records will be deleted.

for more infomation about how to create `DeleteActions` at [MSDN](http://msdn.microsoft.com/enus/library/bb315018.aspx).
