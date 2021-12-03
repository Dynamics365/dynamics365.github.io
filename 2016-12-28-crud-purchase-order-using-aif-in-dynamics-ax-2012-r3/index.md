# CRUD Purchase order using AIF in Dynamics AX 2012 R3


Due to Purchase order doesn’t have Standard document service so we have to create new Document service for that using AIF wizards.

I’m using AIF document service with `NETTCP` or `HTTP` Adapter to Create Purchase order service, here is steps

## Create Query 
with three **datasouce** (`PurchTable, PurchLine, InventDim`) likes below

![](/imagesposts/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3-01.png)

> As best practice for Document service, name of query should be start with Axd* prefix.

## Using AIF Wizards
In AX development environment, go to *Tools > Wizards > AIF document service wizards*

 ![](/imagesposts/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3-02.png)

Choose recent created query and click **next**.

## Create Service operation and `AxBC class`

 ![](/imagesposts/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3-03.png)

Click `next` and then `Generate`.
<!--more-->
You will get service project in **Private** project

## Deploy and create service

 •	Right click on `PurchOrderService` > Add-Ins > Register service

 •	System administration > Setup > Services and AIF > Inbound ports

 •	Click `New` on `Inbound ports form` and name the Service.

 •	Adapter: NETTCP (it also works with HTTP adapter)

 •	In *Service contract customizations* fast tab click `Service operations`

![](/imagesposts/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3-4.png)


`Active` recent created Service  

## Consume service using C#.NET

After service is activated, you can get **WSDL URI** likes 

` http://servername:port/DynamicsAx/Services/PurchaseOrder`

Create console project and Add Service References, the code below
 ![](/imagesposts/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3-05.png)

In Class, I will write method to return list of `EnityKey` PurchId

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
		
`Code for create purchase order `

```C#
var dim = new AxdEntity_InventDim()
{
	InventSiteId = "DN",
	InventLocationId = "F10-S120",
	InventBatchId = "BATCH001"
};

var purchLine = new AxdEntity_PurchLine()
{
	ItemId = "220067",
	PurchQty = 15,
	PurchUnit=  "ea",

	InventDim = new AxdEntity_InventDim[] { dim }
};


var purchTable = new AxdEntity_PurchTable()
{
	OrderAccount = "101-01-75441",
	LanguageId = "en-us",
	CurrencyCode = "KRW",
	PurchName = "AIF PO Test",
	PurchLine = new AxdEntity_PurchLine[] { purchLine }

};

AxdMav_PurchOrderService purchOrder = new AxdMav_PurchOrderService();

purchOrder.PurchTable = new AxdEntity_PurchTable[] { purchTable };

CallContext callContext = new CallContext
{
	Company = "bgr",
	Language = "en-us"
};

Mav_PurchOrderServiceServiceClient client = new Mav_PurchOrderServiceServiceClient();

try
{
	EntityKey[] purchOrderCreatedEntity = client.create(callContext, purchOrder);
	EntityKey purchOrderCreated = (EntityKey)purchOrderCreatedEntity.GetValue(0);

	Console.WriteLine("The purch order created has a Purch ID of " + purchOrderCreated.KeyData[0].Value);
	Console.ReadLine();
}
catch (Exception e)
{
	Console.WriteLine(e.ToString());
	Console.ReadLine();
}
```

`Code for read purchase order `

```C#
EntityKey[] entityKeyList = EntityForPurchId("BGR-000054");

CallContext callContext      = new CallContext();
callContext.Company          = "bgr";
Mav_PurchOrderServiceServiceClient client = new Mav_PurchOrderServiceServiceClient();

AxdMav_PurchOrderService purchOrders = client.read(callContext, entityKeyList);
AxdEntity_PurchTable[] purchTables = purchOrders.PurchTable;
AxdEntity_PurchTable    purchTable = purchTables[0];
AxdEntity_PurchLine purchLine = purchTable.PurchLine[0];

Console.WriteLine("Purch Name: " + purchTable.PurchName);            
Console.WriteLine("Order Account: " + purchTable.OrderAccount);
Console.WriteLine("Language Id: " + purchTable.LanguageId);
Console.WriteLine("Qty: " + purchLine.PurchQty);
Console.WriteLine("Item Id: " + purchLine.ItemId);

client.Close();
Console.ReadLine();
			
```

`Code for update purchase order `

```C#
Mav_PurchOrderServiceServiceClient client = new Mav_PurchOrderServiceServiceClient();
CallContext callContext = new CallContext();
callContext.Company = "bgr";

EntityKey[] entityKeyList = EntityForPurchId("BGR-000078");
AxdMav_PurchOrderService purchOrders = client.read(callContext, entityKeyList);

//salesOrders.GetHashCode();
AxdEntity_PurchTable[] purchTables = purchOrders.PurchTable;
AxdEntity_PurchTable purchTable = new AxdEntity_PurchTable();
purchTable = purchTables.First();

//salesTable.GetHashCode();
AxdEntity_PurchLine purchLine = new AxdEntity_PurchLine();
purchLine = purchTable.PurchLine.First();

decimal purchQty = 20;
purchLine.PurchQty = purchQty;

try
{
	client.update(callContext, entityKeyList, purchOrders);
	EntityKey purchOrdersUpdated = (EntityKey)entityKeyList.GetValue(0);
	Console.WriteLine("The purchase order has been updated has a Purch ID of " + purchOrdersUpdated.KeyData[0].Value + " with Qty " + purchQty.ToString() + "");
	Console.ReadLine();
}
catch (Exception ex)
{
	Console.WriteLine(ex.ToString());
	Console.ReadLine();
}
```
			
`Code for delete purchase order `

```C#
Mav_PurchOrderServiceServiceClient client = new Mav_PurchOrderServiceServiceClient();
CallContext callContext = new CallContext();
callContext.Company = "bgr";

EntityKey[] entityKeyList = EntityForPurchId("BGR-000054");
try
{
	client.delete(callContext, entityKeyList);
	EntityKey purchOrdersDeleted = (EntityKey)entityKeyList.GetValue(0);
	Console.WriteLine("The purch order has been deleted has a purch ID of " + purchOrdersDeleted.KeyData[0].Value);
	Console.ReadLine();
}
catch (Exception ex)
{
	Console.WriteLine(ex.ToString());
	Console.ReadLine();
}
```

Thank you for reading!

