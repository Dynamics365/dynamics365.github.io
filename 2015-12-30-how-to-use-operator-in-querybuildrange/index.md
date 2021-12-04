# How to use operator "or" in QueryBuildRange


This blog post is show how to apply OR conditions in query build ranges in a simple way on same field in a table. Let's see the simple query :

```sql
select * from CustTable where AccountNum == '2001' || AccountNum == '2002'
```

We can find out solutions on [MSDN](https://msdn.microsoft.com/en-us/library/aa893981.aspx)  by using expression in query ranges, but as it has lot of specifications which needs to be followed. However there is a simple way to do it :

```csharp
static void Job12(Args _args)
{
    CustTable cust;
    Query query = new Query();
    QueryBuildDataSource qbds;
    QueryBuildRange queryRange1, queryRange2;
    ;

    qbds = query.addDataSource(tableNum(CustTable));

    queryRange1 = qbds.addRange(fieldNum(CustTable, AccountNum));
    queryRange1.value('1168201');

    queryRange2 = qbds.addRange(fieldNum(CustTable, AccountNum));
    queryRange2.value('9034518');

    info(qbds.toString());
}
```
Result in string format as below image : 

```sql
SELECT * FROM CustTable(CustTable_1) WHERE ((AccountNum = N'1168201') OR (AccountNum = N'9034518'))
```

Thanks for reading :). Happy New Year.  

