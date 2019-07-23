---
title: HOW TO USE "LIKE" OPERATOR IN QUERYBUILDRANGE

date: 2015-11-13 06:57:46.000000000 +07:00
author: Max Nguyen
categories: [ax2012]
tags: [operator, programming]
key: d365
---

To make something as the `LIKE` operator in a query, just assign a value to the QueryRange including a wildcard.


```csharp
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