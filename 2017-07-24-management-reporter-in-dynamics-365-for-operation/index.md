# Management reporter in new AX, Dynamics 365 For Operation


### 1. Overview Management Reporter in New AX
Management Reporter is now **Financial reports**.

As you know Management Reporter is a real-time financial reporting application that is designed to empower information workers to quickly and easily create, generate, secure, and publish financial statements, such as Profit and Loss statements, balance sheets, and cash flow reports. 

In Dynamics 365 For Operation, I can access these reports within AX means directly *from the web client in the browser*. This feature allows me to run financial statements, such as a balance sheet and income statements. 

The **22 default** reports that are included, default financial reports including income statements, balance sheet reports, cash flow statements, and general ledger reports, which can be modified depending on your company’s requirements. 

Here is the list
<figure class='center '>
  <a href="/imagesposts/Financial_reports.png"><img src="/imagesposts/Financial_reports.png" alt=""></a>
</figure>

<!--more-->

### 2. Access
You can find the financial reporting menu in Dynamics 365 For Operation by visiting 

_**General Ledger > Inquiries and reports > Financial reporting.**_

Although, there are some privileges and duties relating to this. These report functions are available to users that have their appropriate privileges and duties assigned to them already.
In order to view, edit, and manage reports in Management Reporter you need to add security administrator role to business Users. Besides, if you only need users can run the financial reports then you have to add the appropriate privileges and duties assigned to them.

Before creating and generating financial reports for a legal entity, you must be done set up for that legal entity:

  * Fiscal calendar
  * Ledger
  * Chart of Accounts
  * Currency

### 3. Management Reporter Benefits
With Management Reporter, business users can:

  * Create their own boardroom-quality reports without IT assistance. 
  * Take advantage of secure report distribution through the centralized Report Library. 
  * Gain strong support for regulatory compliance (Change the report date, currency, view in Summary view or a detailed view, Add either dimension filters or attribute filters).
  * Report design flexibility likes Save dimension combinations, and reuse the dimensions for multiple reports. Management Report Components.
  * Financial report collaboration:
	* Schedule reports so that they are automatically generated on a daily, weekly, monthly, or annual basis.
	* Export to the read-only XPS format, which provides better document security through digital signatures.
	* Export to a Microsoft Excel worksheet.
	* To share reports, you can create email messages that contain links to the reports.

### 4. Management Report Components
Some important components I want to mention here:

  * **Report Designer**
Create report building blocks that can be combined to define and generate a report. The report wizard guides less experienced users through the design process. Advanced users can create new report building blocks or modify existing building blocks to meet their requirements.
  * **Desktop Viewer** 
Used to organize and view reports and supporting files. It also stores the report library. 
  * **Web Viewer** 
Displays Management Reporter reports in a web browser. The Web Viewer does not require an installation of Management Reporter server components. 
  * **Report schedules** 
A user can schedule a single report or a group of reports to generate regularly. 
  * **Management Reporter database**
This SQL database stores the components, known as building blocks, which are used to generate reports. It also stores report definitions and previously generated reports. 
  * **Application service** 
Controls access to the data provider and provides connectivity to clients. 
  * **Process service** 
Generates the reports that are created and queued by the Management Reporter client. 

### 5. Generate financial reports
We are currently at Financial reports screen as picture below

<figure class='center '>
  <a href="/imagesposts/2017-04-10_11h56_15.png"><img src="/imagesposts/2017-04-10_11h56_15.png" alt=""></a>
</figure>

As you can see there's nowhere on the screen or anything that indicates that there's the Management Reporter available, but if we click this `edit` button and entering your email account and password. After you log in, it will open the **Report Definitions** Management Reporter window for us. 

<figure class='center '>
  <a href="/imagesposts/2017-04-10_11h57_20.png"><img src="/imagesposts/2017-04-10_11h57_20.png" alt=""></a>
</figure>

On the left, here we have a list of all the same reports that we were looking at in AX.
Let's look at `the income statement - default report`, So I have a couple parameter fields we need to change before we can generate our report. Please prefer below picture
<figure class='center '>
  <a href="/imagesposts/2017-04-10_12h08_53.png"><img src="/imagesposts/2017-04-10_12h08_53.png" alt=""></a>
</figure>
After we click **generate** button, Financial will be generated on current web browser
<figure class='center '>
  <a href="/imagesposts/2017-04-10_12h11_29.png"><img src="/imagesposts/2017-04-10_12h11_29.png" alt=""></a>
</figure>
we can see here by the report date.  So, on our income statement, we have all the usual fields here.  We have total revenues, gross profit, total operating experiences, net operating income and net income. That all is pretty standard and it all looks good.

### 6. Conclusion
Row, Column, Tree, Report definitions work essentially the same as they did in **Dynamics AX 2012**. I can still use row, column, and tree definitions with multiple reports, and options for the Report definition tabs.

<figure class='center '>
  <a href="/imagesposts/2017-04-10_14h57_51.png"><img src="/imagesposts/2017-04-10_14h57_51.png" alt=""></a>
</figure>

Here are some of the functions that are not available in Financial Reporting for Dynamics 365 for Operations:

  * Publishing/distributing reports to SharePoint and/or SharePoint Online locations is not available.
  * Report Viewer/Library has been removed.
  * Import data from an Excel file is not available.
  * XBRL (extensible Business Reporting Language) is no longer available.
  * Comments are not currently available in the web client.
  * Running a consolidation between companies in different instances of AX is not available.
  * The Wizard has been removed.
  * Management Reporter includes direct integration with the Microsoft Dynamics AX general ledger so that there is no need to create a custom connection to the data source.

Nevertheless, with custom reporting structures, ledger accounts, and dimensions mapping, creation and the capabilities to publish reports to multiple channels and formats from the desktop, and embedding transactional and analytical reports into a customizable dashboard, Financial Reporting in Dynamics 365 For Operation is all set to simplify the financial reporting process, providing instant insight into your financials.

Thank you for reading!
