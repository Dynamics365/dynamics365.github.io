# HOW TO USE "LIKE" OPERATOR IN QueryBuildRange


To make something as the `LIKE` operator in a query, just assign a value to the QueryRange including a wildcard.

```cs
static void QueryBuildRange_Sample(Args _args)
{
    Query                       query = new Query();
    QueryRun                    queryRun;
    QueryBuildDataSource        qbds;
    QueryBuildRange             queryRange;   
    CustTable                   custTable;
    
    qbds.addDataSource(tableNum(CustTable));
    
    queryRange = qbds.addRange(fieldNum(CustTable, AccountNum));
    
    queryRange.value("axd*");
    
    queryRun = new QueryRun(query);
    
    while(queryRun.next())
    {
        custTable = queryRun.get(tableNum(CustTable));
        print custTable.AccountNum;
    }
    pause;
}
```
