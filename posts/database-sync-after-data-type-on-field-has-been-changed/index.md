# Database Sync after data type on field has been changed


You will got an error like this when doing Database Synchronization after changing the data type of the field on the table:

InvalidOperationException: Table ... : Converting Field '..' of Type '..' to '..' is not support. Please drop the original field, sync the table and add new field with same name if needed.
{:.error}

Solution:

* Drop the table and delete references from SQLDictionary

```sql
DROP TABLE AXDB.dbo.NAMEOFTABLE

DELETE
FROM AXDB.dbo.SQLDICTIONARY
WHERE TABLEID IN (
 SELECT TABLEID
 FROM SQLDICTIONARY
 WHERE NAME = 'NAMEOFTABLE'
  AND FIELDID = 0
)
```

* Restart IIS
* In VS 2015, run DB sync again.

Thank you for reading.

