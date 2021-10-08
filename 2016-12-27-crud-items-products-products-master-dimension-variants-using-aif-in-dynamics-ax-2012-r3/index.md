# CRUD Items, products, products master dimension, variants using AIF in Dynamics AX 2012 R3


## Scenarios:
I’m trying to create **product/master product** in Dynamics AX using AIF inbound port, the AIF services consume by C#.NET.

From AX 2012 R2, Item is replaced with Product. Item master was in Inventory Management Module, now there is a separate module for item/product creation `Product information Management`.

Some definitions you should know
There are two types of Products in 2012 they are:

1. **Product**

     _Product information management/Common/Products/Products_

2. **Product Master**

   _Product information management/Common/Products/Products master_

   a. **Variants:**

To create a product variant, you must define at least one product dimension for a product master. You can also rename dimensions.
To create product variants, you must complete the following tasks:

* Set up dimensions, such as size, color, and style.

* Set up variant groups.

* Assign variant groups to a retail hierarchy.

* Create a product master and variants.
<!--more-->
   b. **Product dimensions**

Product dimensions are characteristics that serve to identify a product variant. You can use combinations of product dimensions to define product variants. You must define at least one product dimension for a product master to create a product variant.

## Process:
Normally in AX, we create items master follow process:

*	Create product/product master. 

*	Assigning Dimensions Groups to a Product Master.

*	Create Product dimension combinations (Product Variants)

*	Release product to legal entities

*	Assigning Item Model Group & Item Groups to a Product Master 

## How to do:

Ax provides us standard services for this purpose, so we don’t need to create any custom services for this. I will use 4 services for this purpose, descriptions below

| Service | Purpose |
|:--------|:-------:|
| `EcoResProductService`   | Create products (all types). The service can also be used to                            retrieve data that has already been created (Create Product details in The EcoRes tables). |
|----
| `EcoResProductMasterDimValueService`   | Specify values of product dimensions for a product master. These values become available for the creation of product variants. The service can also be used to retrieve data that has already been created.| 
|----
| `ItemService`   | Release distinct products and product masters. The service can also be used to retrieve data that has already been created.|
|----
| `InventDimCombinationService`   | Release product variants. The service can also be used to retrieve data that has already been created.|
{: rules="groups"}

we have 4 steps

1. Create 4 AIF inbound services against Services operation above and active it
`http://DEV-ERP:8103/DynamicsAx/Services/BCEcoResProduct`
`http://DEV-ERP:8103/DynamicsAx/Services/BCEcoResProductMasterDimValue`
`http://DEV-ERP:8103/DynamicsAx/Services/BCItemsMaster`
`http://DEV-ERP:8103/DynamicsAx/Services/BCInventDimCombination`

2. After services creation, open visual studio then creates new Console project and add service References for that, you will get somethings like pic below:

   ![](/imagesposts/CRUD-Items,-products,-products-master-dimension,-variants-using-AIF-in-Dynamics-AX-2012-R3-01.png)

3. Using C#.Net to consume service

```C#

using ItemsMaster.ItemsRef;
using ItemsMaster.EcoResProductRef;
using ItemsMaster.EcoResProductMasterRef;
using ItemsMaster.InventDimRef;
static void Main(string[] args)
{
	Program master = new Program();
	master.createDistinctProduct();
	Program.releaseProduct();
}

```

`EcoResProductServiceClient`

```C#

public void createDistinctProduct()
{
	AxdEntity_Product_EcoResDistinctProduct distinctProduct = new AxdEntity_Product_EcoResDistinctProduct()
	{
		DisplayProductNumber = "MAX00002",
		ProductType = AxdEnum_EcoResProductType.Item,
		SearchName = "Max",
	   
	};

	distinctProduct.Translation = new AxdEntity_Translation[1];
	distinctProduct.Translation[0] = new AxdEntity_Translation()
	{
		LanguageId = "en-us",
		Name = "Max Nguyen"
	};

	distinctProduct.Identifier = new AxdEntity_Identifier[1];
	distinctProduct.Identifier[0] = new AxdEntity_Identifier()
	{
		ProductNumber = "MAX00002"
	};

	distinctProduct.StorageDimGroup = new AxdEntity_StorageDimGroup[1];
	distinctProduct.StorageDimGroup[0] = new AxdEntity_StorageDimGroup()
	{
		Product = "MAX00002",
		StorageDimensionGroup = "SW_P"
	};

	distinctProduct.TrackingDimGroup = new AxdEntity_TrackingDimGroup[1];
	distinctProduct.TrackingDimGroup[0] = new AxdEntity_TrackingDimGroup()
	{
		Product = "MAX00002",
		TrackingDimensionGroup = "Batch Only"
	};

	AxdEcoResProduct product = new AxdEcoResProduct()
	{
		Product = new AxdEntity_Product_EcoResProduct[1] { distinctProduct }
	};

	EcoResProductRef.CallContext EcoResProductSctx = new EcoResProductRef.CallContext()
	{
		Company = "bgr",
		Language = "en-us",
	};

	EcoResProductRef.EcoResProductServiceClient ecoResProductSClient = new EcoResProductRef.EcoResProductServiceClient();
	
	ecoResProductSClient.create(EcoResProductSctx, product);

}

```

`EcoResProductServiceClient`

```C#

public void createMaster()
{
	AxdEntity_Product_EcoResProductMaster productMaster = new AxdEntity_Product_EcoResProductMaster()
	{
		DisplayProductNumber = "MAX00002",
		ProductType = AxdEnum_EcoResProductType.Item,
		SearchName = "Max Nguyen"
	};

	productMaster.Translation = new AxdEntity_Translation[1];
	productMaster.Translation[0] = new AxdEntity_Translation()
	{
		LanguageId = "en-us",
		Name = "Max Nguyen"
	};

	productMaster.Identifier = new AxdEntity_Identifier[1];
	productMaster.Identifier[0] = new AxdEntity_Identifier()
	{
		ProductNumber = "MAX00002"
	};
	productMaster.ProductDimGroup = new AxdEntity_ProductDimGroup[1];
	productMaster.ProductDimGroup[0] = new AxdEntity_ProductDimGroup()
	{
		Product = "MAX00002",
		ProductDimensionGroup = "MAX10"
	};
	productMaster.VariantConfigurationTechnology = AxdEnum_EcoResVariantConfigurationTechnologyType.PredefinedVariants;

	AxdEcoResProduct axdProduct = new AxdEcoResProduct()
	{
		Product = new AxdEntity_Product_EcoResProduct[1] { productMaster }
	};

	EcoResProductRef.CallContext EcoResProductSctx = new EcoResProductRef.CallContext()
	{
		Company = "bgr",
		Language = "en-us",
	};

	EcoResProductRef.EcoResProductServiceClient ecoResProductSClient = new EcoResProductRef.EcoResProductServiceClient();
	
	ecoResProductSClient.create(EcoResProductSctx, axdProduct);

}

```

`EcoResProductMasterDimValueServiceClient`

```C#

static void createMasterDimensions()
{

	EcoResProductMasterRef.AxdEntity_EcoResSize ecoResSizeL = new EcoResProductMasterRef.AxdEntity_EcoResSize()
	{
		Name = "L"
	};

	EcoResProductMasterRef.AxdEntity_EcoResSize ecoResSizeM = new EcoResProductMasterRef.AxdEntity_EcoResSize()
	{
		Name = "M"
	};
	//master dimensions definition (two sizes, L and M)
	AxdEntity_MasterDim_EcoResProductMasterSize sizeDimensionL = new AxdEntity_MasterDim_EcoResProductMasterSize()
	{
		SizeProductMaster = "MAX00002",
		Size = "L",
		EcoResSize = new EcoResProductMasterRef.AxdEntity_EcoResSize[1] { ecoResSizeL }
	};
	AxdEntity_MasterDim_EcoResProductMasterSize sizeDimensionM = new AxdEntity_MasterDim_EcoResProductMasterSize()
	{
		SizeProductMaster = "MAX00002",
		Size = "M",
		EcoResSize = new EcoResProductMasterRef.AxdEntity_EcoResSize[1] { ecoResSizeM }

	};

	AxdEcoResProductMasterDimValue axdDimValue = new AxdEcoResProductMasterDimValue()
	{
		MasterDim = new AxdEntity_MasterDim_EcoResProductMasterDimensionValue[2] { sizeDimensionL, sizeDimensionM }
	};

	EcoResProductMasterRef.CallContext masterDimctx = new EcoResProductMasterRef.CallContext();
	EcoResProductMasterDimValueServiceClient masterDimensionService = new EcoResProductMasterDimValueServiceClient();
	try
	{
		masterDimensionService.create(masterDimctx, axdDimValue);
	}
	catch (Exception e)
	{
		System.Console.WriteLine(e.Message);
		System.Console.ReadKey();
	}
}

```


`EcoResProductServiceClient`

```C#

static void createVariant()
{
	//product variant definition
	AxdEntity_Product_EcoResDistinctProductVariant productVariant = new AxdEntity_Product_EcoResDistinctProductVariant()
	{
		DisplayProductNumber = "MAXL",
		ProductType = AxdEnum_EcoResProductType.Item,
		SearchName = "MAXL",
		ProductMaster = "MAX00002"
	};
	productVariant.Translation = new AxdEntity_Translation[1];
	productVariant.Translation[0] = new AxdEntity_Translation()
	{
		LanguageId = "en-us",
		Name = "Max L size"
	};
	productVariant.VariantDimValue = new AxdEntity_VariantDimValue_EcoResProductVariantDimensionValue[1];
	productVariant.VariantDimValue[0] = new AxdEntity_VariantDimValue_EcoResProductVariantSize()
	{
		DistinctProductVariant = "MAXL",
		ProductDimensionAttribute = 3173,//The ID of the EcoResSize table
		Size = "L",
		EcoResSize = new EcoResProductRef.AxdEntity_EcoResSize[1]
		{
			new EcoResProductRef.AxdEntity_EcoResSize() { Name = "L" }
		}
	};

	AxdEcoResProduct axdProduct = new AxdEcoResProduct()
	{
		Product = new AxdEntity_Product_EcoResProduct[1] { productVariant }
	};
	EcoResProductRef.CallContext inventDimctx = new EcoResProductRef.CallContext();
	EcoResProductServiceClient productService = new EcoResProductServiceClient();
	try
	{
		productService.create(inventDimctx, axdProduct);
	}
	catch (Exception e)
	{
		System.Console.WriteLine(e.Message);
		System.Console.ReadKey();
	}
}

```

`this method can use to release a distinct product or a product master`

```C#

public static void releaseProduct()
{
	var invent = new AxdEntity_Invent()
	{
		ItemId = "MAX00002",
		UnitId = "box"
	};

	var purch = new AxdEntity_Purch()
	{
		ItemId = "MAX00002",
		UnitId = "box"
	};

	var sales = new AxdEntity_Sales()
	{
		ItemId = "MAX00002",
		UnitId = "box"
	};

	var inventTable = new AxdEntity_InventTable()
	{
		ItemId = "MAX00002",
		PmfProductType = AxdEnum_PmfProductType.MainItem,
		Product = "MAX00002",
		Invent = new AxdEntity_Invent[] { invent },
		Purch = new AxdEntity_Purch[] { purch },
		Sales = new AxdEntity_Sales[] { sales },
		
	};

	var item = new AxdItem()
	{
		InventTable = new AxdEntity_InventTable[1] { inventTable }
	};

	ItemsRef.CallContext callContext = new ItemsRef.CallContext();
	ItemsRef.ItemServiceClient client = new ItemsRef.ItemServiceClient();

	callContext.Company = "bgr";
	callContext.Language = "en-us";

	try
	{
		ItemsRef.EntityKey[] itemCreatedEntity = client.create(callContext, item);
		ItemsRef.EntityKey itemCreated = (ItemsRef.EntityKey)itemCreatedEntity.GetValue(0);

		Console.WriteLine("AxdEntity_Invent " + itemCreated.KeyData[0].Value);
		Console.ReadLine();
	}
	catch (Exception e)
	{
		Console.WriteLine(e.ToString());
		Console.ReadLine();
	}
}

```

`Release product Variants`

```C#

static void releaseProductVariants()
{
	AxdEntity_InventDimCombination releasedVariant = new AxdEntity_InventDimCombination()
	{
		DistinctProductVariant = "MAX00002",
		ItemId = ""
	};

	AxdInventDimCombination inventDimCombination = new AxdInventDimCombination()
	{
		InventDimCombination = new AxdEntity_InventDimCombination[1] { releasedVariant }
	};

	InventDimRef.CallContext itemsctx = new InventDimRef.CallContext()
	{
		Company = "bgr",
		Language = "en-us"
	};
	InventDimCombinationServiceClient inventDimCombinationService = new InventDimCombinationServiceClient();
	try
	{
		inventDimCombinationService.create(itemsctx, inventDimCombination);
	}
	catch (Exception e)
	{
		System.Console.WriteLine(e.Message);
		System.Console.ReadKey();
	}
}

```

Just for example, in `main` I only create Distinct product and release it, but you can use another method to create master, variant, masterDim and so on.

    4. Try to run it and here is a result

![](/imagesposts/CRUD-Items,-products,-products-master-dimension,-variants-using-AIF-in-Dynamics-AX-2012-R3-02.png)

Thank you for reading and feel free to give me a question.


