# Insert, update, Delete order line through AIF In AX 2012 R3


In previous [post](https://dynamics365.github.io/ax2012/integration/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3/), I already show how to create purchase order through AIF with `NETTCP` or `HTTP adapter`. In this post, we will get little deep more about action on line of order.

The following code sample shows how to `insert`, `update`, `delete` a line of an existing purchase order through AIF, currently I’m using C#.NET console project for demo.

As Partial update, we must include just the fields to change and any fields required by the document (you can check `Data policies` in AIF Service ports form for that).

Also, notice how action properties are specified – no matter we do with the line, which means updating the order.

I’m giving you an idea how it looks like, here is the code

`The first method will handle the key of AIF Service`


```C#
private static EntityKey[] EntityForPurchId(string purchId)
{
	KeyField field = new KeyField()
	{
		Field = "PurchId",
		Value = purchId
	};

	EntityKey key = new EntityKey()
	{
		KeyData = new[] { field }
	};

	return new[] { key };
}

```

<!--more-->
`create Line, delete Line, update line in Purchase order`

```C#
EntityKey[] entityKeyList = EntityForPurchId("BGR-000054");

CallContext callContext = new CallContext();
callContext.Company = "bgr";

Mav_PurchOrderServiceServiceClient client = new Mav_PurchOrderServiceServiceClient();
AxdMav_PurchOrderService purchOrders = client.read(callContext, entityKeyList);

//Define which line need to be update or delete
var lastLine = purchOrders.PurchTable[0].PurchLine.Last();

var purchLine = new AxdEntity_PurchLine()
{
	ItemId = "110329",
	PurchQty = 1,
	CurrencyCode = "KRW",
	RecIdSpecified      = true,
	LineNumberSpecified = true,
	action              = AxdEnum_AxdEntityAction.create,
	actionSpecified     = true

	//for delete
	//RecId               = lastLine.RecId,
	//RecIdSpecified      = true,
	//action              = AxdEnum_AxdEntityAction.delete,
	//actionSpecified     = true

	//for Update
	//RecId               = lastLine.RecId,
	//RecIdSpecified      = true,
	//action              = AxdEnum_AxdEntityAction.update,
	//actionSpecified     = true
	
};

var purchTable = new AxdEntity_PurchTable()
{
	_DocumentHash = purchOrders.PurchTable[0]._DocumentHash,
	OrderAccount = "101-01-75441",
	LanguageId = "en-us",
	CurrencyCode = "KRW",
	PurchName = "AIF PO Test",
	action = AxdEnum_AxdEntityAction.update,
	actionSpecified = true,
	PurchLine = new[] { purchLine }
};

AxdMav_PurchOrderService purchOrder = new AxdMav_PurchOrderService()
{
	PurchTable = new AxdEntity_PurchTable[] {purchTable}
};


client.update(callContext, entityKeyList, purchOrder);

```


Thank you for reading!

