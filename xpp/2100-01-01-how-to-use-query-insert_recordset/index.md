# How to use query::insert_recordset?


How we can use the Insert_RecordSet function with Query, which is one of the batch SQL operations we frequently use in X++.

I want to insert two fields of all records in DmrBillsIncomingHeader table into DmrBillsIncominTest table. I have to create a Query and query the DmrBillsIncomingHeader table.

Also, which fields I want to insert, I have to mark them using Map. I should have written it with the classical method as follows.

```insert_recordset billsIncominTest (BillsIncomingId, Description  )
        select BillsIncomingId, Description from DmrBillsIncomingHeader ;```

Now let's write this with Query.

```C#
internal final class DmrFDJobQueryInsertRecordList
{
    public static void main(Args _args)
    {
        DmrBillsIncominTest     billsIncominTest;
        Map                     fieldMap;
        Query                   q;
        QueryBuildDataSource    qbdsBillsIncomingHeader;
        QueryBuildFieldList     flBills;
 
        delete_from billsIncominTest;
 
        q = new Query();
        qbdsBillsIncomingHeader = q.addDataSource(tableNum(DmrBillsIncomingHeader));
 
        flBills = qbdsBillsIncomingHeader.fields();
        flBills.addField(fieldNum(DmrBillsIncomingHeader, BillsIncomingId));
        flBills.addField(fieldNum(DmrBillsIncomingHeader, Description));
 
        fieldMap = new Map(Types::String, Types::Container);
        fieldMap.insert( fieldStr(DmrBillsIncominTest, BillsIncomingId), [qbdsBillsIncomingHeader.uniqueId(),
                                    fieldStr(DmrBillsIncomingHeader, BillsIncomingId)]);
        fieldMap.insert(fieldStr(DmrBillsIncominTest, Description), [qbdsBillsIncomingHeader.uniqueId(),
                                    fieldStr(DmrBillsIncomingHeader, Description)]);
 
        query::insert_recordset(billsIncominTest, fieldMap, q);
 
    }
 
}
```
