# Link Type and Join Types in ax 2012


## Link Type

**Active**:
`Parent and child` data source is updated immediately when a new record in the parent data source is selected. Continuous updates consume lots of resources consuming.

**Delayed**:
`Parent and child` A pause is inserted before linked child data sources are updated. This enables faster navigation in the parent data source because the records from child data sources are not updated immediately.

For example, you can scroll a list of orders where you do not want to review the lines associated with the order until you stop scrolling.

**Passive**: `Parent and child` Linked child data sources are not updated automatically. Updates of the child data source must be programmed on the `active()` method of the master data source.

## Join Types

**InnerJoin** `Combined data source` select the record from the main table that matches records in the joined table and vice versa.

```sql
//X++
select AccountNum from custTable
    join TaxGroupId from custGroup
    where custGroup.CustGroup == custTable.CustGroup;
//CROSS JOIN in T-SQL:
SELECT T1.ACCOUNTNUM, T1.RECID, T2.TAXGROUPID, T2.RECID
FROM CUSTTABLE T1 CROSS JOIN CUSTGROUP T2
WHERE ((T1.PARTITION=?) AND (T1.DATAAREAID=?))
  AND (((T2.PARTITION=?) AND (T2.DATAAREAID=?)) 
   AND (T2.CUSTGROUP=T1.CUSTGROUP))
```

There is one record for each match. Records without related records in the other data source are eliminated from the result.

**Outer Join**: `Combined data source` select the records from the main table. The records are retrieved whether they have matching records in the joined table

```sql
//X++:
select AccountNum from custTable
    outer join AccountID from custBankAccount
    where custBankAccount.CustAccount == custTable.AccountNum;
//LEFT OUTER JOIN in T-SQL:
SELECT T1.ACCOUNTNUM, T1.RECID, T2.ACCOUNTID, T2.RECID
FROM CUSTTABLE T1 LEFT OUTER JOIN CUSTBANKACCOUNT T2
ON (((T2.PARTITION=?) AND (T2.DATAAREAID=?))
AND (T1.ACCOUNTNUM=T2.CUSTACCOUNT))
WHERE ((T1.PARTITION=?) AND (T1.DATAAREAID=?))
```

**Exist Join**: `Combined data source`The data source retrieves a record from the main table for each matching record in the joined table.

```sql
//X++:
select AccountNum from custBankAccount
    exists join custTable
    where custBankAccount.CustAccount == custTable.AccountNum;
//EXISTS (SELECT 'x'...) in T-SQL:
SELECT T1.ACCOUNTNUM, T1.RECID
FROM CUSTBANKACCOUNT T1
WHERE ((T1.PARTITION=?) AND (T1.DATAAREAID=?))
AND EXISTS (SELECT 'x'
            FROM CUSTTABLE T2
            WHERE (((T2.PARTITION=?) AND (T2.DATAAREAID=?))
              AND  (T1.CUSTACCOUNT=T2.ACCOUNTNUM)))
```

*The differences between InnerJoin and ExistJoin are as follows:*

+ When the join type is **_ExistJoin_**, the search ends after the first match has been found.

+ When the join type is **_InnerJoin_**, all matching records are searched for.

+ **_NotExistJoin_**: `Combined data source` Select records from the main table that do not have a match in the joined table.

