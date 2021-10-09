# Create Purchase Orders - Confirm - Product receipt - Post using X++ in Dynamics 365 Finance & Operations


> The script will work for Dynamics 365 Finance & Operations version

<!--more-->

```cs
class MaxGeneratePO
{
    public static void main(Args _args)
    {
        int i = 0; // number of purchase orders
        NumberSeq numberSeq;
        PurchTable purchTable;
        PurchLine purchLine;
        InventDim inventDim;

        while (i <= 3)
        {
            ttsBegin;
            MaxGeneratePO createPO = new MaxGeneratePO();
            numberSeq = NumberSeq::newGetNum(PurchParameters::numRefPurchId());
            numberSeq.used();
            purchTable.PurchId = numberSeq.num();
            purchTable.initValue();
            purchTable.initFromVendTable(VendTable::find('US-101'));

            if (!purchTable.validateWrite())
            {
                throw Exception::Error;
            }

            purchTable.insert();
            inventDim.clear();
            purchLine.clear();
            purchLine.initValue();

            purchLine.PurchId = purchTable.PurchId;
            purchLine.ItemId = 'D0002';
            inventDim.InventSiteId = "1";
            inventDim.InventLocationId = "11";
            purchLine.InventDimId=InventDim::findOrCreate(inventDim).inventDimId ;
            purchLine.createLine(true, true, true, true, true, true);

            purchLine.PurchQty = 5;
            purchLine.PurchUnit = "ea";
            purchLine.PurchPrice = createPO.randomAmount(); // get random amount nubmer
            purchLine.LineAmount = purchLine.calcLineAmount();
            purchLine.update();
            
            //PO confirm
            PurchFormLetter purchFormLetter;
            PurchFormLetter purchFormLetterPack;
            purchFormLetter = PurchFormLetter::construct(DocumentStatus::PurchaseOrder);
            purchFormLetter.update(purchTable,
                                strFmt("ConNum_%1", purchTable.PurchId),
                                systemDateGet(),
                                PurchUpdate::All,
                                AccountOrder::None,
                                NoYes::No,
                                NoYes::no);
            //Product receipt
            createPO.proceed(purchTable.PurchId, purchLine.ItemId,purchLine.InventDimId,purchLine.PurchQty,strFmt("RptNum_%1", purchTable.PurchId));
            //Post PO
            createPO.postPOInvoice(purchTable.PurchId, strFmt("RptNum_%1", purchTable.PurchId));

            info(strFmt("Purchase order '%1' has been created", purchTable.PurchId));
            ttsCommit;
            i++;
        }
    }

    public boolean proceed(PurchId _purchId, ItemId _itemId,inventDimId _inventDimId, PurchQty _qty, PackingSlipId _productReceiptNumber)
    {
        return
            this.generateProductReceipt(_purchId, this.addToPurchLineList(_purchId, _itemId, _inventDimId, _qty), _productReceiptNumber);
    }

    public boolean generateProductReceipt(PurchId _purchId, List _purchLineList, PackingSlipId _productReceiptNumber)
    {
        boolean ret = true;
        PurchFormLetter purchFromLetter;
        PurchTable purchTable = PurchTable::find(_purchId);

        try
        {
            ttsbegin;
            purchFromLetter = PurchFormLetter::construct(DocumentStatus::PackingSlip);
            purchFromLetter.createFromLines(true);
            purchFromLetter.parmLineList(_purchLineList.pack());
            purchFromLetter.update(purchTable, _productReceiptNumber,
                                DateTimeUtil::getToday(DateTimeUtil::getUserPreferredTimeZone()),
                                PurchUpdate::All);
            ttscommit;
        }
        catch
        {
            ret = false;
        }
        return ret;
    }

    public List addToPurchLineList(PurchId _purchId, ItemId _itemId,inventDimId _inventDimId, PurchQty _qty)
    {
        List purchLineList = new List(Types::Record);
        PurchLine purchLine = PurchLine::findItemIdInventDimId(_purchId, _itemId, _inventDimId);

        if(purchLine && _qty > 0)
        {
            purchLine.PurchReceivedNow = _qty;
            purchline.modifiedField(fieldNum(PurchLine, PurchReceivedNow));
            purchLineList.addEnd(purchLine);
        }
        return purchLineList;
    }

    public void postPOInvoice(PurchId purchId, PackingSlipId packingSlipId)
    {
        TmpFrmVirtual               tmpFrmVirtualVend;
        PurchFormLetter_Invoice     purchFormLetter;
        VendPackingSlipJour         vendPackingSlipJour;
        SysQueryRun                 chooseLinesQuery;
        SysQueryRun                 chooseLinesPendingInvoiceQuery;
        container                   conTmpFrmVirtual;
        List                        selectedList    = new List(Types::Record);

        select firstonly vendPackingSlipJour
                    where vendPackingSlipJour.PurchId == purchId
            && vendPackingSlipJour.PackingSlipId == packingSlipId;

        if (vendPackingSlipJour)
        {
            tmpFrmVirtualVend.clear();
            tmpFrmVirtualVend.TableNum  = vendPackingSlipJour.TableId;
            tmpFrmVirtualVend.RecordNo  = vendPackingSlipJour.RecId;
            tmpFrmVirtualVend.NoYes     = NoYes::Yes;
            tmpFrmVirtualVend.Id        = vendPackingSlipJour.PurchId;
            tmpFrmVirtualVend.insert();
        }

        chooseLinesQuery = new SysQueryRun(queryStr(PurchUpdate));
        chooseLinesQuery.query().addDataSource(tableNum(VendInvoiceInfoTable)).enabled(false);

        // chooseLinesPendingInvoiceQuery needs to be initialized, although it will not be used
        chooseLinesPendingInvoiceQuery = new SysQueryRun(queryStr(PurchUpdatePendingInvoice));
        chooseLinesPendingInvoiceQuery.query().dataSourceTable(tableNum(PurchTable)).addRange(fieldNum(PurchTable,PurchId)).value(queryValue(''));
           
        purchFormLetter = PurchFormLetter::construct(DocumentStatus::Invoice);
        purchFormLetter.chooseLinesQuery (chooseLinesQuery);
        purchFormLetter.parmQueryChooseLinesPendingInvoice(chooseLinesPendingInvoiceQuery);
        purchFormLetter.purchTable (PurchTable::find(PurchId));
        purchFormLetter.transDate (systemDateGet());
        purchFormLetter.parmParmTableNum (strFmt("%1",packingSlipId)); //This is invoice number
        purchFormLetter.printFormLetter (NoYes::No);
        purchFormLetter.sumBy (AccountOrder::Auto);
        purchFormLetter.specQty (PurchUpdate::PackingSlip);
      
        while select tmpFrmVirtualVend
        {
            selectedList.addEnd(tmpFrmVirtualVend);
            conTmpFrmVirtual = selectedList.pack();
        }
        purchFormLetter.selectFromJournal(conTmpFrmVirtual);
        purchFormLetter.reArrangeNow(true);
        purchFormLetter.run();
    }

    public int randomAmount()
    {
        RandomGenerate randomGenerate;
        randomGenerate = RandomGenerate::construct();
        randomGenerate.parmSeed(new Random().nextInt());
        return RandomGenerate.randomInt(100, 800);
    }

}
```

Thank you for reading.

