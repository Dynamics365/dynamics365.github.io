---

title: How to block vendor and send email for notification.
description: Using batch to find and block vendor base on last transaction condition and notify for them by emails.
author: Max Nguyen
modified: 2017-01-10
categories: [ax2012, trick, tools, integration]
tags: [xpp, batch]
key: d365
---

Main requirements is Using batch to find and block vendor base on last transaction condition and notify for them by emails.

### Set up E-mail parameters

For set up email, we need Go to AX `System administrator > Setup > E-mail parameters`

![]({{site.url}}/assets/imagesposts/Email-parameters.png)

<!--more-->

### Vendor emails

Vendor emails locate on `LogisticsElectronicAddress.Locator`, `partyTable.PrimaryContactEmail`, `partyLocation.Location`, please take a look on this job to find how to update Vendor emails and you also could see the relations more clearly.

{% highlight csharp %}
static void UpdateVendorEmail(Args _args)
{
    VendTable                   vendTable;
    LogisticsElectronicAddress  electronicAddress;
    DirPartyTable               partyTable;
    DirPartyLocation            partyLocation;

    electronicAddress.initValue();
    electronicAddress.Type = LogisticsElectronicAddressMethodType::Email;
    electronicAddress.Description = "Max Nguyen";
    electronicAddress.Locator     = "luan52@outlook.com";
    electronicAddress.IsPrimary   = NoYes::Yes;
    electronicAddress.insert();

    while select forUpdate partyTable
        exists join vendTable
            where vendTable.Party == partyTable.RecId
    {
        ttsBegin;
        partyTable.PrimaryContactEmail = electronicAddress.RecId;
        partyTable.update();
        ttsCommit;

        select firstOnly forupdate partyLocation
            where partyLocation.Party == partyTable.RecId;

        if (partyLocation)
        {
            ttsBegin;
            partyLocation.Location = electronicAddress.Location;
            partyLocation.update();
            ttsCommit;
        }
        else
        {
            partyLocation.initValue();
            partyLocation.Location = electronicAddress.Location;
            partyLocation.Party     = partyTable.RecId;
            partyLocation.insert();
        }
    }
}
{% endhighlight %}

### Batch class

Main logic here is find Vend accounts are not exist in `VendTrans` table with condition `endTrans.TransDate >= beginDate`, and `beginDate` count from today `systemDateGet()`.

{% highlight csharp %}
public class Max_VendorBlockedBatch extends RunBaseBatch
{
}
{% endhighlight %}

Get the date before 6 months from today

{% highlight csharp %}
public TransDate getBeginDate()
{
    TransDate   beginDate;
    TransDate   currentDate;
    Months      month;
    YearBase    years;
    Day         day;

    currentDate = systemDateGet();
    day         = dayOfMth(currentDate);
    month       = mthOfYr(currentDate);
    years       = year(currentDate);
    if (month < 6)
    {
        beginDate = mkDate(day, 12 - (6 - month) + 1, years - 1);
    }
    else
    {
        beginDate = mkDate(day, month - 6 + 1, years);
    }

    return beginDate;
}
{% endhighlight %}

Send E-mail

{% highlight csharp %}

public void sendEmail(AccountNum _vendor, str _recipient)
{
    str                                     sender  = 'sender@email.com';
    str                                     subject = 'Account blocked';
    str                                     body    = 'Your account is blocked due to last transaction';
    List                                    toList;
    ListEnumerator                          le;
    Set                                     permissionSet;
    System.Exception                        e;
    str                                     mailServer;
    int                                     mailServerPort;
    System.Net.Mail.SmtpClient              mailClient;
    System.Net.Mail.MailMessage             mailMessage;
    System.Net.Mail.MailAddress             mailFrom;
    System.Net.Mail.MailAddress             mailTo;
    System.Net.Mail.MailAddressCollection   mailToCollection;
 
    try
    {
        toList = strSplit(_recipient, ';');
         
        permissionSet = new Set(Types::Class);
        permissionSet.add(new InteropPermission(InteropKind::ClrInterop));
        CodeAccessPermission::assertMultiple(permissionSet);
 
        mailServer      = SysEmaiLParameters::find(false).SMTPRelayServerName;
        mailServerPort  = SysEmaiLParameters::find(false).SMTPPortNumber;
        mailClient      = new System.Net.Mail.SmtpClient(mailServer, mailServerPort);
 
        le = toList.getEnumerator();
        le.moveNext();
         
        mailFrom    = new System.Net.Mail.MailAddress(sender);
        mailTo      = new System.Net.Mail.MailAddress(strLTrim(strRTrim(le.current())));
        mailMessage = new System.Net.Mail.MailMessage(mailFrom, mailTo);     

        mailToCollection = mailMessage.get_To();
        while (le.moveNext())
        {
            mailToCollection.Add(strLTrim(strRTrim(le.current())));
        }
         
        mailMessage.set_Priority(System.Net.Mail.MailPriority::High);
        mailMessage.set_Subject(subject);
        mailMessage.set_Body(body);
 
        mailClient.Send(mailMessage);
        mailMessage.Dispose();
 
        CodeAccessPermission::revertAssert(); 
        info(strFmt('Email was sent to vendor %1.', _vendor));
    }
    catch (Exception::CLRError)
    {
        e = ClrInterop::getLastException();
        while (e)
        {
            info(e.get_Message());
            e = e.get_InnerException();
        }

        CodeAccessPermission::revertAssert();
    }
}

{% endhighlight %}

Initializes a new instance of the *<c>Batch</c>* class.

{% highlight csharp %}
public static MAX_VendorBlockedBatch construct()
{
    return new MAX_VendorBlockedBatch();
}
{% endhighlight %}

Gets description of the dialog.

{% highlight csharp %}
public static ClassDescription description()
{
    return 'Vendor blocked batch';
}
{% endhighlight %}

Find the vendor without transaction and disable, then send email to vendor

{% highlight csharp %}
public void run()
{
    VendTrans   vendTrans;
    VendTable   vendTable;
    TransDate   beginDate;
    Email       email;
    int         i;

    try
    {
        beginDate = this.getBeginDate();
        while select forUpdate AccountNum, Party from vendTable
            Notexists join vendTrans
            where vendTrans.AccountNum == vendTable.AccountNum
                && vendTrans.TransDate >= beginDate
        {
            //Set the vendor blocked
            ttsBegin;
            vendTable.Blocked = CustVendorBlocked::All;
            vendTable.update();
            ttsCommit;

            //Send E-mail to vendor
            email = vendTable.email();
            if (email)
            {
                this.sendEmail(vendTable.AccountNum, email);
            }
            else
            {
                warning(strFmt('The vendor %1 did not have E-mail address.', vendTable.AccountNum));
            }
        }
    }
    catch (Exception::Deadlock)
    {
        retry;
    }
}
{% endhighlight %}

Provides an enter point for the <c>Batch</c> class.

{% highlight csharp %}
public static void main(Args _args)
{
    MAX_VendorBlockedBatch vendorBlockedBatch = MAX_VendorBlockedBatch::construct();

    if (vendorBlockedBatch.prompt())
    {
        vendorBlockedBatch.run();
    }
}
{% endhighlight %}

From here you can run class and set up recurrence for batch job.

Thank you for reading!