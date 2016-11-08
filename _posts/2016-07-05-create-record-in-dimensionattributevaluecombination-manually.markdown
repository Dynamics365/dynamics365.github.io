---
title: Create record in DimensionAttributeValueCombination table manually.
layout: post
date: 2016-07-05 18:47:00 +09:00
author: Max Nguyen
categories: [ax2012, trick]
tags: [xpp, programming, fidim]
---

Sometime we need this `RecId` value in `DimansionAttributeValueCombination` Table for some reasons likes putting new dimension in `LedgerDimension` field in `LedgerJournalTrans` Table, or somewhere else.

Suppose my Account structure likes: MainAcct - Dept - Woker - SubAcct - Item
you can custom base on your struture

This code is for creating a record into `DimansionAttributeValueCombination` in code and then get RecID of this. 
 
```csharp
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


