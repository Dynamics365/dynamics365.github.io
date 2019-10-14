---
date: 2019-10-10 00:00:00+00:00
title: Extend the standard reports in Dynamics 365 finance and operations 
tags:
- COC
- Extension
- SSRS
author: Max Nguyen
key: 2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations
cover: /assets/images/test2.jpg
---

In this article, I will show the procedure for the SSRS Reports development and customization in Dynamics 365 finance and operations (Version 8.1 and above).

The scenario is you would like to extend **the customer account statement** report with 2 main tasks

	• Create custom design for the report. Ref https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/analytics/custom-designs-business-docs?toc=/dynamics365/commerce/toc.json
	• Expand the standard report data sets. Ref https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/analytics/expand-app-suite-report-data-sets?toc=/dynamics365/commerce/toc.json

Same with AX 2012 version, there is no change on how you developer a new SSRS report with query based and report data provider. But if you want to extend or modify the standard one you need to understand how to use extensions in general, Event handlers and Chain of Command.

The steps

1. The main temp table is **CustAccountStatementExtTmp**, right click and create an extension; I'm going to add a new string field **MaxTxT**

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_1.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_1.png" alt=""></a>
</figure>

2. Duplicate the **CustAccountStatementExt** report in *the Application explorer > AOT > Reports > CustAccountStatementExt* as shown in below screen shot:

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_2.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_2.png" alt=""></a>
</figure>

Rename the report and provide any appropriate name: **MaxCustAccountStatementExt**

3. Modify the report design, right click on report dataset and choose restore to refresh the new field

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_3.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_3.png" alt=""></a>
</figure>

Open report designer and add that field into a table

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_4.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_4.png" alt=""></a>
</figure>

4. Create a new Extension class that extends the standard report controller class.

***class MaxCustAccountStatementExtController_Ext extends CustAccountStatementExtController{}***

{% highlight csharp %}
//Add construct
public static MaxCustAccountStatementExtController_Ext construct()
{
    return new MaxCustAccountStatementExtController_Ext();
}
{% endhighlight %}

Copy the main method from the standard controller class and add references to the new Controller class

{% highlight csharp %}
public static void main(Args _args)
{
    SrsPrintMgmtFormLetterController controller = new MaxCustAccountStatementExtController_Ext();
    controller.parmReportName(PrintMgmtDocType::construct(PrintMgmtDocumentType::CustAccountStatement).getDefaultReportFormat());
    controller.parmArgs(_args);
    MaxCustAccountStatementExtController_Ext::startControllerOperation(controller, _args);
}
 
protected static void startControllerOperation(SrsPrintMgmtFormLetterController _controller, Args _args)
{
    _controller.startOperation();
}
{% endhighlight %}

Optional method, determine which default design for report

{% highlight csharp %}
protected void outputReport()
{
    SRSCatalogItemName  reportDesign;
    reportDesign = ssrsReportStr(MaxCustAccountStatementExt,Report);
    this.parmReportName(reportDesign);
    this.parmReportContract().parmReportName(reportDesign);
    formletterReport.parmReportRun().settingDetail().parmReportFormatName(reportDesign);
    super();
}
{% endhighlight %}

5. Create new report handler class

***class MaxCustAccountStatementHandler ***

We have two different ways to Populate the data in the Report handler class:
* Add a temp table Inserting event, row-by-row calculations.

{% highlight csharp %}
[DataEventHandlerAttribute(tableStr(CustAccountStatementExtTmp), DataEventType::Inserting)]
public static void CustAccountStatementExtTmpInsertEvent(Common c, DataEventArgs e)
{
    CustAccountStatementExtTmp    tempTable = c;
    tempTable.MaxTxT = "Hello world";
}
{% endhighlight %}

* Add a data processing post-handler, inserting operations that use a single pass over the result set of the standard solution.

{% highlight csharp %}
[PostHandlerFor(classStr(CustAccountStatementExtDP), methodstr(CustAccountStatementExtDP, processReport))]
public static void TmpTablePostHandler(XppPrePostArgs arguments)
{
    CustAccountStatementExtDP dpInstance = arguments.getThis() as CustAccountStatementExtDP;
    CustAccountStatementExtTmp tmpTable = dpInstance.getCustAccountStatementExtTmp();
    ttsbegin;
    while select forUpdate tmpTable
    {
        tmpTable.MaxTxT = "Hello world";
        tmpTable.update();
    }
    ttscommit;
}
{% endhighlight %}

6. Add a delegate handler method to start to use your custom report. In this example, extend the ***getDefaultReportFormatDelegate*** method in the ***PrintMgtDocTypeHandlerExt*** class by using the following code.

{% highlight csharp %}
class MaxPrintMgtDocTypeHandlersExt
{
    [SubscribesTo(classstr(PrintMgmtDocType), delegatestr(PrintMgmtDocType, getDefaultReportFormatDelegate))]
    public static void getDefaultReportFormatDelegate(PrintMgmtDocumentType _docType, EventHandlerResult _result)
    {
        switch (_docType)
        {
            case PrintMgmtDocumentType::CustAccountStatement:
                _result.result(ssrsReportStr(MaxCustAccountStatementExt, Report));
                break;
        }
    }
}
{% endhighlight %}

7. Create extension for the existing menu items: navigating to the **CustAccountStatementExt** output menu item and create extension.

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_5.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_5.png" alt=""></a>
</figure>

Also make sure to set the value of the Object property to **MaxCustAccountStatementExtController_Ext** to redirect user navigation to the extended solution.

That's all, this is what you should have 

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_9.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_9.png" alt=""></a>
</figure>

8. Update the Print management settings to use the custom business document

Go to *Account payable > Inquiries and reports > Setup > Forms > Form setup*
Click **Print Management**, find the document configuration settings, and then select the custom design

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_6.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_6.png" alt=""></a>
</figure>

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_7.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_7.png" alt=""></a>
</figure>

9. Run report and test the result

<figure class='center '>
  <a href="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_8.png"><img src="{{site.url}}/assets/imagesposts/2019-10-10-Extend-the-standard-reports-in-Dynamics-365-finance-and-operations_8.png" alt=""></a>
</figure>