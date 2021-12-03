# AIF Custom response value in Dynamics AX 2012 R3


For this demonstration, I use AIF service to create Sales order with `SalesSalesOrderService` and I gonna consume AIF using C#.NET.

Normally, in consume service application we handle return value by using `EntityKeyList`, `EntityKey`, `KeyData[0].Value`. And for Sales Order It will return Created SalesID.

Let's take a look on `AxdSalesOrder` class. More about `Axd` class please prefer [this](https://technet.microsoft.com/en-us/library/aa862063.aspx).

Then go to `createList` method, this method will handle response value

```C#
public AifEntityKeyList createList(
    AifDocumentXml                  _xml,
    AifEndpointActionPolicyInfo     _actionPolicyInfo,
    AifConstraintListCollection     _constraintListCollection)
{
    AifEntityKeyList aifEntityKeyList;

    aifEntityKeyList = super(_xml, _actionPolicyInfo, _constraintListCollection);

    // Sales orders are committed - master planning explosion can be executed and confirmed dates be set
    this.postSalesOrderCreation(aifEntityKeyList);

    return aifEntityKeyList;
}
```

So, how about customer wants another meaning value beside `SalesID` likes `InventTransId` information in `SalesLine` table or another tables base on your requirement. To do that, we need to customize this method.

<!--more-->

### How to do

I will use `Map` and `MapEnumerator` classes (Please refer MSDN to understand these class), this is steps:

1. We get the `SalesId` from the original `entityKeyList` and add into `Map`.
2. add `Map` to `MapEnumerator`.
3. Use `SalesId` to find the sales line. (`mapEnumerator.currentValue()` can get current `SalesID`).
4. Create a new `entityKey` and insert the sales line information to this `entityKey`.
5. add back `entityKey` to `entityKeyList`

From here in `entityKeyList` will store information of `SalesID` and List fields of `SalesLine` table.

Here is the code for `createList` method, beside `SalesId` I will try to get `InventTransId` in `SalesLine` Table

```C#

public AifEntityKeyList createList(
    AifDocumentXml                  _xml,
    AifEndpointActionPolicyInfo     _actionPolicyInfo,
    AifConstraintListCollection     _constraintListCollection)
{
    AifEntityKeyList aifEntityKeyList;
    
    SalesId                         salesId;
    SalesLine                       salesLine;
    AifEntityKey                    entityKey;
    AifEntityKey                    salesEntityKey;
    Map                             keyDataMap;
    Map                             salesOrderMap;
    MapEnumerator                   mapEnumerator;

    aifEntityKeyList = super(_xml, _actionPolicyInfo, _constraintListCollection);

    // Sales orders are committed - master planning explosion can be executed and confirmed dates be set
    this.postSalesOrderCreation(aifEntityKeyList);

    entityKey       = aifEntityKeyList.getEntityKey(1);
    keyDataMap      = entityKey.parmKeyDataMap();
    mapEnumerator   = keyDataMap.getEnumerator();
    while (mapEnumerator.moveNext())
    {
        salesId = mapEnumerator.currentValue();
        if (salesId)
        {
            while select InventTransId, RecId from salesLine
                where salesLine.SalesId == salesId
            {
                salesEntityKey = new AifEntityKey();
                salesOrderMap  = new Map(Types::Integer, Types::Container);
                salesEntityKey.parmTableId(tableNum(SalesLine));
                salesEntityKey.parmRecId(salesLine.RecId);
                salesOrderMap.insert(fieldNum(SalesLine, InventTransId), [salesLine.InventTransId]);
                salesEntityKey.parmKeyDataMap(salesOrderMap);
                aifEntityKeyList.addEntityKey(salesEntityKey);
            }
        }
    }

    return aifEntityKeyList;
}

```

Code in C#.NET to get list key

```C#

SalesOrderServiceClient client = new SalesOrderServiceClient();
try
{
	EntityKey[] salesOrderCreatedEntity = client.create(callContext, salesOrder);
	//EntityKey salesOrderCreated = (EntityKey)salesOrderCreatedEntity.GetValue(0);

	System.Collections.IEnumerator enumerator = salesOrderCreatedEntity.GetEnumerator();

	while (enumerator.MoveNext())
	{
		EntityKey salesOrderCreated = (EntityKey)enumerator.Current;
		Console.WriteLine(salesOrderCreated.KeyData[0].Field);
		Console.WriteLine(salesOrderCreated.KeyData[0].Value);
	}
	//Console.WriteLine("The sales order created has a Sales ID of " + salesOrderCreated.KeyData[0].Value);
	Console.ReadLine();
}
catch (Exception e)
{
	Console.WriteLine(e.ToString());
	Console.ReadLine();
}
```

Here is what we got

![](/imagesposts/AIF-Custom-response-value-in-Dynamics-AX-2012-R3.png)

Just remember this one just for AIF Document Standard, for AIF custom service we do another way I will so in next post.

Thank you for reading.
