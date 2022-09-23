# Create record in DimensionAttributeValueCombination table manually.


Sometime we need this `RecId` value in `DimansionAttributeValueCombination` Table for some reasons likes putting new dimension in `LedgerDimension` field in `LedgerJournalTrans` Table, or somewhere else.

Suppose my Account structure likes: _**MainAcct - Dept - Woker - SubAcct - Item**_,
you can custom base on your struture.

This code is for creating a record into `DimansionAttributeValueCombination` in code and then get `RecID` of this. 

```c#
static void createDimensionAttributeValueCombination(Args _args)
{
    DimensionAttributeValueContract     ValueContract;
    DimensionAttributeValueCombination  davc;
    MainAccount                         valueMainAccount;
    DimensionStorage                    dimStorage;

    DimensionServiceProvider DimensionServiceProvider   = new DimensionServiceProvider();
    LedgerAccountContract LedgerAccountContract         = new LedgerAccountContract();
    List ListValueContract                              = new List(Types::Class);
    
    // Get main account with structure : Mainacct - DE - woker - subacct - item
    valueMainAccount = MainAccount::findByMainAccountId('910001'); //Get main account

    ValueContract = new DimensionAttributeValueContract();
    ValueContract.parmName('Department') ;
    ValueContract.parmValue('6020'); //Value for dimension Department
    ListValueContract.addEnd(ValueContract);

    ValueContract = new DimensionAttributeValueContract();
    ValueContract.parmName('Worker') ;
    ValueContract.parmValue('000001'); //Value for dimension ExpenseCode
    ListValueContract.addEnd(ValueContract);

    ValueContract = new DimensionAttributeValueContract();
    ValueContract.parmName('SubAcct') ;
    ValueContract.parmValue('교보생명'); //Value for dimension Project
    ListValueContract.addEnd(ValueContract);

    ValueContract = new DimensionAttributeValueContract();
    ValueContract.parmName('Item') ;
    ValueContract.parmValue('110329'); //Value for dimension Project
    ListValueContract.addEnd(ValueContract);

    LedgerAccountContract.parmMainAccount(valueMainAccount.MainAccountId);
    LedgerAccountContract.parmValues(ListValueContract);

    //if combination is not exist then create new one
    dimStorage = DimensionServiceProvider::buildDimensionStorageForLedgerAccount(LedgerAccountContract);
    davc = DimensionAttributeValueCombination::find(dimStorage.save());
    info(strFmt("RecId : %1 - DisplayValue : %2", davc.RecId, davc.DisplayValue));
}
```

or you can use this way

```c#
static void createDimension(Args _args)
{
    Struct      struct = new Struct();
    container   financialDimension;
    LedgerDimensionAccount legder;

    //// Get main account with structure : Mainacct - DE - woker - subacct - item
    struct.add('Department', '6020');
    struct.add('Item', '220006');
    struct.add('SubAcct', 'Max test account');
    struct.add('Worker', '000002');

    financialDimension += struct.fields();
    financialDimension += struct.fieldName(1);
    financialDimension += struct.valueIndex(1);
    financialDimension += struct.fieldName(2);
    financialDimension += struct.valueIndex(2);
    financialDimension += struct.fieldName(3);
    financialDimension += struct.valueIndex(3);
    financialDimension += struct.fieldName(4);
    financialDimension += struct.valueIndex(4);

    legder = DimensionDefaultingService::serviceCreateLedgerDimension(
                            DimensionStorage::getDefaultAccountForMainAccountNum("910001"),
                            AxdDimensionUtil::getDimensionAttributeValueSetId(financialDimension));
    info(strFmt("%1",legder));

}
```

