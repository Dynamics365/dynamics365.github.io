# Posting cust payment journal using X++


Base on `JournalId` on `LedgerJournalTable` you can use code below to <span style="color: red">post the transactions</span>

```c#
//Contract class
[
    DataMemberAttribute('gJournalId'),
    SysOperationDisplayOrderAttribute('1')
]
public LedgerJournalId parmJournalNum(LedgerJournalId _journalId = gJournalId)
{
    gJournalId = _journalId;

    return gJournalId;
}

//Processing class
[SysEntryPointAttribute]
public void process(MAV_PostCustPaymentJourContract _contract)
{
    LedgerJournalTable      ledgerJournalTable;
    LedgerJournalCheckPost  postCustPaymentJournal;

    ledgerJournalTable = LedgerJournalTable::find(_contract.parmJournalNum());
    if (ledgerJournalTable)
    {
        postCustPaymentJournal = LedgerJournalCheckPost::newLedgerJournalTable(ledgerJournalTable, NoYes::Yes);
        postCustPaymentJournal.run();
    }
}
```
