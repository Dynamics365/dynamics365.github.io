---
title: posting Cust payment journal using X++

date: 2016-12-26 00:00:00.000000000 +9:00
author: Max Nguyen
categories: [ax2012, trick]
tag: [trick, programming, posting]
key: d365
---

Base on `JournalId` on `LedgerJournalTable` you can use code below to <span style="color: red">post the transactions</span>

{% highlight csharp %}
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
{% endhighlight %}