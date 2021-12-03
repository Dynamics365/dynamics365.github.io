# CRUD Purchase Requisition using AIF in Dynamics AX 2012 R3


The workflow process moves purchase requisitions through the review process, from an initial status of **Draft** to a final status of **Approved**. When a purchase requisition is submitted for review, the workflow process is started. After a purchase requisition is approved, a purchase order can be generated for the purchase requisition lines and submitted to the vendor for order fulfillment.

We will use AIF standard service for import PR from outside, service name `PurchReqImportService`.

in `AxPurchReqTable` class and `setPurchReqId()` method

```C#
protected void setPurchReqId()
{
    NumberSequenceReference numberSequenceReference;

    if (this.isMethodExecuted(funcName()))
    {
        return;
    }

    if (this.isFieldSetExternally(fieldNum(PurchReqTable, PurchReqId)))
    {
        if (this.isSetMethodsCalledFromSave())
        {
            if (!this.purchReqTable())
            {
                numberSequenceReference = PurchReqTable::numRefPurchReqId();
                this.checkNumber(numberSequenceReference.numberSequenceTable(),fieldNum(PurchReqTable,PurchReqId),this.parmPurchReqId());
                if (numberSequenceReference.NumberSequenceId && numberSequenceReference.numberSequenceTable().Continuous)
                {
                    NumberSeq::newReserveNum(numberSequenceReference).reserve(this.parmPurchReqId());
                }
            }
        }
    }
    else
    {
        if (this.isFieldSet(fieldNum(PurchReqTable, PurchReqId)))
        {
            return;
        }

        if (!this.parmPurchReqId())
        {
            if (this.isSetMethodsCalledFromSave())
            {
                //this.parmPurchReqId(NumberSeq::newGetNum(PurchParameters::numRefPurchReqId()).num());
                numberSequenceReference = PurchReqTable::numRefPurchReqId();
                if(numberSequenceReference)
                {
                    this.setField(fieldNum(PurchReqTable, PurchReqId), NumberSeq::newGetNum(PurchParameters::numRefPurchReqId()).num());
                }
                else
                {
                    this.setField(fieldNum(PurchReqTable, PurchReqId), this.parmExternalSourceID());
                }
            }
        }
    }
}

```

Base on this method, you could know how System get PurchReqId.

<!--more-->

### How to do

Go to Inbound ports form to create new service with `NETTCP` adapter, choose service operations likes below:

![](/imagesposts/CRUD-Purchase-Requisition-using-AIF-in-Dynamics-AX-2012-R3-1.png)


Then `active` AIF inbound service

### Consume Pruchase requisition service 

Open visual studio and create new console project.

![](/imagesposts/CRUD-Purchase-Requisition-using-AIF-in-Dynamics-AX-2012-R3-2.png)

Add service reference

`http://DEV-ERP:8101/DynamicsAx/Services/MavPurchaseRequisition`

![](/imagesposts/CRUD-Purchase-Requisition-using-AIF-in-Dynamics-AX-2012-R3-3.png)

![](/imagesposts/CRUD-Purchase-Requisition-using-AIF-in-Dynamics-AX-2012-R3-4.png)

This one just for demo, so I just create code base on required fields of AIF.

![](/imagesposts/required_fields.png)

Here is the code in `main` method

```C#

PurchReqImportServiceClient client = new PurchReqImportServiceClient();
CallContext context = new CallContext()
{
	Company = "BGR",
	Language = "En-us"
};

AxdEntity_PurchReqLine purchReqLine = new AxdEntity_PurchReqLine()
{
	Requisitioner = "000007",
	BuyingLegalEntity = "BGR",
	ItemId = "110329",
	PurchUnitOfMeasure = "Box",
	CurrencyCode = "KRW",
	PurchQty = 100,
	PurchQtySpecified = true,
	PriceUnit = 1,
	PriceUnitSpecified = true,
};

// Create an instance of the document class.
AxdEntity_PurchReqTable purchReqTable = new AxdEntity_PurchReqTable()
{
	PurchReqId = "",
	PurchReqName = "Purch Req by Max",
	ExternalSourceID = "PR002",
	ExternalSourceName = "PR002",
	AutoSubmitToWorkflowRequired = AxdEnum_NoYes.No,
	StatusToBeSaved = AxdEnum_PurchReqCreationStatus.Draft,
	RequisitionStatus = AxdEnum_PurchReqRequisitionStatus.Draft,
	RequisitionStatusSpecified = true,
	RequiredDate = new DateTime(2016, 12, 30),
	RequiredDateSpecified = true,
	TransDate = new DateTime(2016, 12, 30),
	TransDateSpecified = true,
	PurchReqLine = new AxdEntity_PurchReqLine[1] { purchReqLine }
};


// Create instances of the entities that are used in the service and
// set the needed fields on those entities.
AxdPurchReqImport purchReq = new AxdPurchReqImport()
{
	PurchReqTable = new AxdEntity_PurchReqTable[1] { purchReqTable }
};

try
{
	client.create(context, purchReq);
}
catch (Exception e)
{
	Console.WriteLine(e.ToString());
	Console.ReadLine();
}

```

Run it and check result in AX

![](/imagesposts/CRUD-Purchase-Requisition-using-AIF-in-Dynamics-AX-2012-R3-5.png)

Please prefer previous [post](https://dynamics365.github.io/ax2012/integration/CRUD-Purchase-order-using-AIF-in-Dynamics-AX-2012-R3/) for another operations

Thank you for reading!

