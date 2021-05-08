---
date: 2021-01-01 00:00:00+00:00
title: AUTOMATE YOUR TESTS FOR DYNAMICS 365 FINANCE AND OPERATIONS
tags:
- Dynamics 365 Finance
- Dynamics 365 Supply Chain Management
- Dynamics 365 Commerce
- AUTOMATED TEST
author: Max Nguyen
key: AUTOMATE-YOUR-TESTS-FOR-DYNAMICS-365-FINANCE-AND-OPERATIONS
cover: /assets/imagesposts/How-to-upgrade-to-Visual-Studio-2017-3.png
hidden: true
---



A new article for you the community. More specifically, all project managers, functional and technical consultants, as well as customer-side decision-makers on the deployment of Dynamics 365 Finance and Operations.

I told myself that it would be important to offer you a global article on all the testing capabilities (manual or automatic) for Dynamics 365 Finance and Operations. It is the result of quite a long work but which I hope will help you to improve your code delivery and upgrade processes.

As you know, updates to version 10.x are monthly and therefore require operational tests on a regular basis ! I don’t know for you, but for some customers that I know it can take almost 1 week or more for 3 consultants…

In addition, if you want to be in an agile process of continuous delivery in production, it is often necessary to take time with the Key Users or the IT team to ensure that we do not have a regression in any part of the ERP. The more development you add, the longer and more difficult it will be to test all of your processes: this will therefore lower the quality of your deployments and repetitions of bugs in the chain: which will necessarily have a cost but also psychological in trust of the tool and your team.

For my part, I know that this part is often the least urgent in a process, however if this is done from the start of your GoLive and in continuity, you will gain enormously, therefore automating your tests as much as possible to allow time your team to manage more strategic cases (training, designs) that a machine will not be able to automate. This is the motto of the PowerAutomate tool of the PowerPlatform :

> “Take Care of what’s important. Automate the rest”

Before going into the details of each testing feature that you can use with Dynamics 365 for Finance and Operations, it is important to clarify a few things, such as :

*   You CAN’T test everything… well automatically in fact. As you will see in this article, testing some SSRS Reports, layouts, interfaces or other tools will be very complicated to achieve, and a machine is not a human… So keep in mind that all these features will help you to automate **as much as possible** long tests process, but you will need to do something… Like of course analyzing Plan test reports, see errors log, correct them, tests manual process : but at the end, maybe you will gain 90% of your testing time ! So clearly I invite you to use all the elements that I will present to you.
    
*   I will not go very deeper in each parts, because it will be too long ! So of course, you can go directly in each Microsoft documentations to go further in details. But keep in mind, that all documentations are presented separately and it seemed important to me to report everything to you in a single document.
    
*   **Data integration testing**
    
    Do not use RSAT (as you will see after) for integration tests, instead rely on the data management framework (also known as DIXF). [The Data task automation framework](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/data-task-automation?WT.mc_id=BA-MVP-5003744) enables you to configure and automate the testing of your data integration scenarios.
    

* * *

Now, we can go !

So, in Dynamics 365 Finance and Operations, you have **3 parts** of testing.

While the functional validation of an ERP application can’t be fully data agnostic, there are multiple phases and approaches for testing. These testing phases include:

*   SysTest framework
    
*   ATL frameowrk
    
*   Regression Suite Automation Tool (RSAT)
    

**Overview**
------------

*   **SysTest framework** – The SysTest framework is reliable for writing unit tests. Because unit tests are generally testing a method or function, they should always be data agnostic and dependent only on the input data that is provided as part of the test.
    
*   **ATL framework** – Microsoft has an ATL framework that is an abstraction on the SysTest framework and makes functional test writing much more simple and reliable. This framework should be used for writing component tests or simple integration tests.
    
*   **RSAT** – The RSAT is used for integration tests and business cycle tests. The business cycle tests, also called the regression validation tests, are dependent on existing data. However, these tests can become data agnostic if you consider additional factors.
    
    *   Where unit tests and component tests are low level and can fully be data agnostic (not dependent on existing dataset), the business cycle or regression validation tests are dependent on some existing data. This data includes setup, configuration settings (parameters), and master data (customer, vendors, items, etc.), but never transaction data. Make sure that during the test, if any of these are being changed, that they are reverted back as part of the final test.
        
    *   Select master data based on certain criteria instead of selecting a particular record. For example, if you want to select an item based on its dimension values and stock availability, filter the product list with those values, select the first item, and copy the number to be used for future tests. If it’s a simple master data line such as customer, vendor, or item, it can be created as part of the automation and used in future tests through chaining.
        
    *   Enter the unique identifiers, such as invoice numbers, through the number sequence or by using Microsoft Excel functions such as =TEXT(NOW(),"yyyymmddhhmm"). This function will provide a unique number every minute, which allows you to track when the action happened. This can be used for variables such as product receipt numbers and vendor invoice numbers. These tests continue to work on the same database again and again, without requiring any restoration.
        
    *   Always set the **Edit mode** of the environment to **Read** or **Edit** as the first test case because the default option is **Auto**. The **Auto** options always uses the previous setting and can cause unreliable tests. You can change it in the TEST account that will be used in RSAT (User Option)
        
    *   Only validate after you filter on a particular transaction instead of generic validation. For example, for the number of records, filter for the transaction number or the transaction date so that the validation excludes all other transactions.
        
    *   If you are checking a customer balance or budget check, save the value first and then add your transaction value to validate the expected result instead of validating a fixed expected value.
        

![rsat-data-agnostic-testing-01.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587332348623-GGBMM6240KC2RLYZBIGC/ke17ZwdGBToddI8pDm48kMo2oFNaRhep3CETKsVD6FdZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpzVaYBSdEGKX3Z2Ji0rkewp53JitqEEk_iHjARLo0MODrVv31zgdOUs4syoHnmwl8Q/rsat-data-agnostic-testing-01.png)

![rsat-data-agnostic-testing-01.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/rsat-data-agnostic-testing-01.png)

* * *

**1/ LCS <=> BPM <=> AzureDevOps (via Task Recorder)**
------------------------------------------------------

You aren't required to use the Business process modeler (BPM) tool in LCS. However, BPM is the recommended tool if you want to enable the management and distribution of test libraries across projects and tenants. These capabilities are especially useful for Microsoft partners and independent software vendors (ISVs). BPM enables the distribution of test libraries as part of LCS solutions. If you are not using BPM, you can manually create test cases in Azure DevOps and attach developer recording files to your Azure DevOps test cases. You can create developer recording files directly from the Task recorder pane.

On my side, I will show you how to create BPM in LCS before going to record all my process with the Task Recorders.

So, go to your LCS project first. I will assume also that you have already connect LCS and Azure DevOps together.

You can after go to the Business Process Modeler part.

![Screenshot 2020-04-20_21-19-53-857.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587410442388-5WZ982QLN9R2HK4W6YIW/ke17ZwdGBToddI8pDm48kFOp38lKI3QVVd-xz559hkcUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcTTkQvpthtDvCyJAFbXpEjQkgZ_egD7MGsUHwYPYusIhQwPe4WxjleEbImOF0bCaG/Screenshot+2020-04-20_21-19-53-857.png)

![Screenshot 2020-04-20_21-19-53-857.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_21-19-53-857.png)

  
As you will notice here, you have your own BPM and also some done by Microsoft. Of course, in your own company/customer, it’s somehow complicated to have a standard & global but it will be helpful to check the Microsoft BPM to see how it works.

![Screenshot 2020-04-20_21-21-34-605.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587410731693-0THZOUXOO30S0SDL69CA/ke17ZwdGBToddI8pDm48kN5bZgWCJOZ4UwIbBmGbvz8UqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYwL8IeDg6_3B-BRuF4nNrNcQkVuAT7tdErd0wQFEGFSnCudTIVYgJWnHjFLX87nlS5kFl2zekI1Fx0swwzFdQIZf24zI4Q-L3HBwzaE6nkd_w/Screenshot+2020-04-20_21-21-34-605.png)

![Screenshot 2020-04-20_21-21-34-605.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_21-21-34-605.png)

On my side I will create a complete new one to handle testing purpose. “Normally” before a GoLive it’s a task very highly recommended to do with your Key Users, Project Manager and Functional Consultant.

![Screenshot 2020-04-20_21-25-13-784.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587410780368-HMWNF598Q6ZDM7NSZTLE/ke17ZwdGBToddI8pDm48kMx751RqM_9w3VYV6TtzURh7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0kBIzfKk1xe2aJwtZsm5jqRp0CnJLxJ58VFKG2vfrmyriAM3z7WlT24aIpyOc28GRg/Screenshot+2020-04-20_21-25-13-784.png)

![Screenshot 2020-04-20_21-25-13-784.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_21-25-13-784.png)

So as you can see, I’ve created a very basic one, just for an example. My process is only to model my customer creation process.

After you can add other child flows, dependent processes etc…

Also in BPM, you can have a VISIO model of Flow charts, of all your process in the ERP, has defined in LCS. It can be also a good way to add requirements in AzureDevOps before jumping to do customization. Clearly it’s high level definition, but useful also to not forget some specific process, as well as doing some tutorials practices based on that : as you will see also in Task Recorder, you can do your training documentation with screenshots. That’s why if you do it at the very beginning stage of your implementation, all these process conception will help you not just for testing purpose after or before GoLive !!  
If you want to learn more, you’ll to go there in the Microsoft documentation below :

[Learn more on BPM in LCS](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/bpm-overview?WT.mc_id=BA-MVP-5003744)

Now that I’ve done my BPM in LCS, i will need to go to FinOps instance to record all my process flow.

**_Quick tips_** : Download the Extension to have the featue for Google Chrome to take screenshots (good way for training documentation) :

[https://chrome.google.com/webstore/detail/d365-for-finance-and-oper/inifapcodikhojbnbafaalgbgkfmnlob/related?hl=en-GB](https://chrome.google.com/webstore/detail/d365-for-finance-and-oper/inifapcodikhojbnbafaalgbgkfmnlob/related?hl=en-GB)

Go like in an UAT instance, where you have some DEMO data in it or maybe already a copy of your production/live database. Of course, the mandatory data is to have all your reference and master data in it in order to have a whole complete process. (like for my customer flow : Customer Group, Tax Group etc…)

When you are in FinOps, go to the top right !

![Screenshot 2020-04-20_21-43-54-968.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587411848037-RZ3R28K5VEC81PL9673S/ke17ZwdGBToddI8pDm48kD5VYYvKcT-R-nHRtxVVsw9Zw-zPPgdn4jUwVcJE1ZvWEtT5uBSRWt4vQZAgTJucoTqqXjS3CfNDSuuf31e0tVE88cbCSReamZczIfd1QtgG1jMFzXi9AgLsC_rQtkLeZj-3CTWZQ124CTRPXn-dnvM/Screenshot+2020-04-20_21-43-54-968.png)

![Screenshot 2020-04-20_21-43-54-968.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_21-43-54-968.png)

Click on “Create recording”

Give it a name and description if you want. (good if you have multiple different type of flows to create a customer) - activate take screenshots option if you also intend to generate documentation, in addition to being able to recover the trace file for automated tests.

![Screenshot 2020-04-20_21-48-41-193.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587412276501-A24NMEGQH8TLQE5CFJ7Z/ke17ZwdGBToddI8pDm48kMdG36Wab3YeVnq8__O4gpJZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpzxjTVrOcfhmB267ug3qlsmtVegmw6FxsAWPAayk_XXWwvKjodss21d9xgoHIdT-pA/Screenshot+2020-04-20_21-48-41-193.png)

![Screenshot 2020-04-20_21-48-41-193.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_21-48-41-193.png)

Well, next, you need to do your process :)

After end your task recorder, and keep in a safe place your .AXTR files ; like in a Sharepoint folder.

![Screenshot 2020-04-20_21-54-08-943.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587412564336-W395Q1EZWXXO1P36CHEG/ke17ZwdGBToddI8pDm48kM_h-J2QWxBfivtQ0d30sbMUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8GRo6ASst2s6pLvNAu_PZdK7fG-WDrcZT3BusQ57y9oewMS1m91ndvXUVpVpqAXy7Ma31gESfx6Z3MWorx7aAS0/Screenshot+2020-04-20_21-54-08-943.png)

![Screenshot 2020-04-20_21-54-08-943.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_21-54-08-943.png)

**Keep in mind that Task Records will need to be adjust in your lifecycle of your project, maybe if you change your process or maybe because you have added a new custom field.**  
And here are few more best practices :

Follow these guidelines when authoring and recording your test cases, especially if you are planning to automate test execution. The process and tools described in this article apply to business process acceptance tests. They are not meant to replace component and unit testing that is typically owned by developers.

*   Author a limited number of test cases that, when combined, cover complete end-to-end processes.
    
*   Focus on business processes that have been customized.
    
*   An individual test case (recording) should cover one or two business tasks only, typically executed by one person. This simplifies task recording maintenance. Do not combine a complete end-to-end business process such as "Procure to Pay" or "Order to Cash" into one large task recording. For example, instead of having RFQ > Purchase Order > Product Receipt > Vendor Invoice > Vendor Payment as one test case, divide the process into three or four test cases. You will have the opportunity to combine these tests into an ordered test suite later.
    
*   A test case should have at least one validation. Try to validate critical fields that cover the impact of other fields. For example: Validation of totals on sales or purchase orders cover the unit price/quantity/discount/tax ...etc.
    
*   Avoid printing a report in a test case. If a test case needs to print a report, it should be selected on screen.
    
*   80+% of test cases should be of transactions or source documents. Master data should be limited to up to 20% of test cases only.
    

  
Saying that, let’s go again in our BPM library that we have created earlier. We will attach our task recorder file in it.

For that, just click on upload.  

![Screenshot 2020-04-20_22-01-29-423.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587412909221-V5A4UU4NUJS2NKB1NI9M/ke17ZwdGBToddI8pDm48kPf0vW68QdB7OgWk2WnYPE57gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UXXz2C_GG8RCMBj0cgOAL37a71c7VBldkGpAXjUl8z5spC969RuPXvt2ZwyzUXQf7Q/Screenshot+2020-04-20_22-01-29-423.png)

![Screenshot 2020-04-20_22-01-29-423.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-01-29-423.png)

Now we will sync it directly to our AzureDevOps Project, to create all our test plan and create every unit test case for every process that you have attached a task recorder file.

You will need to click on the 3 dots, right after the Collapse button and click on the two like me :

![Screenshot 2020-04-20_22-03-46-975.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587413064126-KEPIN4IPJFCLSG4K2JRW/ke17ZwdGBToddI8pDm48kFoE2TQ6B0h4430aY4pFJ_tZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIPmS97EvE3DMzmDlTQ7QUTaenPoKL_aBu1BDPJunoiVg/Screenshot+2020-04-20_22-03-46-975.png)

![Screenshot 2020-04-20_22-03-46-975.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-03-46-975.png)

Now you can go to your Azure DevOps / VSTS tenant.

You will need an account that have a MSDN subscription or at least a Basic Licence + test plan in order to do automate testing. So take 1 or 2 licences, essentially for the account that will configure all test plan. The basic licence is at 5 euro/month/user and the basic + test plan is more than 40 euro/month/user so not the same cost, but compared to the gain in effort by continuing to carry out only manual tests, believe me you will necessarily largely recover that…

I have created a Test Plan called : RSAT for the tool Regression Suite Automation Tool that I will explain after. But you can call it more with a friendly name, like “Regression test for 10.0.9 upgrade”. Of course it’s like a folder of every test case suite that we will attach after, to have a global overview of every tests cases.

It will be also here that you can see Chart, progression testing report, based on a time purpose, priority, logs. You can copy/past a whole test plan, every week-month, like for upgrade process.

Attach like me the test cases that are normally been in VSTS, since you have synced in LCS before in the BPM.

![Screenshot 2020-04-20_22-13-33-236.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587413627880-T4ML01PZP0REC1IGEZIB/ke17ZwdGBToddI8pDm48kIh7Tc64H97usHr-xULEPzMUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYwL8IeDg6_3B-BRuF4nNrNcQkVuAT7tdErd0wQFEGFSnLE-OtbA_Ov1e1cUNsgsLbzvGPd5w0dX4yB_tk0MPX8Fb5cm_bGBpquh7jB7UWWyXw/Screenshot+2020-04-20_22-13-33-236.png)

![Screenshot 2020-04-20_22-13-33-236.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-13-33-236.png)

You will see in a test case, like the Customer flow that I’ve done before some informations. The tag “LCS:TestCases” in order to quickly see all synced tests with BPM in LCS. You will see every steps actions, and in attachment you will have the .AXTR file needed for automate the test with RSAT after.

![Screenshot 2020-04-20_22-17-06-855.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587413839607-69HABW9UEGGHJ1AOIX8A/ke17ZwdGBToddI8pDm48kMxcG0IExh--WxzpkRgCK8kUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYwL8IeDg6_3B-BRuF4nNrNcQkVuAT7tdErd0wQFEGFSnACZ8DzcvSL2CGaqcb2zJfJxOO8S0E_1IigijB4_gJqZPxNWsU7-Ftt5oyTwJlXfmQ/Screenshot+2020-04-20_22-17-06-855.png)

![Screenshot 2020-04-20_22-17-06-855.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-17-06-855.png)

* * *

**2/ RSAT - Regression Suite Automation Tool**
----------------------------------------------

**Overview**

The Regression suite automation tool (RSAT) significantly reduces the time and cost of user acceptance testing (UAT). UAT is typically required before you take a Microsoft application update, or before you apply custom code and configurations to your production environment. RSAT lets functional power users record business tasks by using Task recorder and then convert the recordings into a suite of automated tests, without having to write source code. For more information about Task recorder, see [Task recorder resources](https://www.powerazure365.com/user-interface/task-recorder).

RSAT is fully integrated with Microsoft Azure DevOps for test execution, reporting, and investigation. Test parameters are decoupled from test steps and stored in Microsoft Excel files.

RSAT usage is described here in this schema :

![end-to-end.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587418937828-LODFMN18JDT5C9W58OGJ/ke17ZwdGBToddI8pDm48kLYKXKHRktXcq-N6k1IDIxEUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYy7Mythp_T-mtop-vrsUOmeInPi9iDjx9w8K4ZfjXt2dluKPZoPbXJeCMQnWJ5wLN7w-84gilVaAiyNEB0bvuCWCjLISwBs8eEdxAxTptZAUg/end-to-end.png)

![end-to-end.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/end-to-end.png)

First, you will need to install it :)

Windows 10 and needed also Excel app.

[Download RSAT](https://www.microsoft.com/download/details.aspx?WT.mc_id=BA-MVP-5003744&id=57357)

But before going to RSAT directly, go back to your Azure DevOps, you will need a Personal Access Token.

![Sans titre.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587419873591-FBKG0U00CDNSEX1LLXHE/ke17ZwdGBToddI8pDm48kN9pu7RPYxRB7TaL8tuc5iBZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIW4dfKkW9eBPcapIY2NzTqIRq-Y5q872Fa4ByvrGUPng/Sans+titre.png)

![Sans titre.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Sans+titre.png)

![Screenshot 2020-04-20_23-51-45-050.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587419916146-O6RVCKHRDLZRGW17EIF2/ke17ZwdGBToddI8pDm48kGd3Y0PUHdz42VR16pNrk5wUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYy7Mythp_T-mtop-vrsUOmeInPi9iDjx9w8K4ZfjXt2drXrG-eCPfcNJrfr3bSseU77m0pW8sjhPPRdH-BqfSAfCjLISwBs8eEdxAxTptZAUg/Screenshot+2020-04-20_23-51-45-050.png)

![Screenshot 2020-04-20_23-51-45-050.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-51-45-050.png)

Create a new one for RSAT, and make him as expiration date in 1 year, to be safe :)

After you need to configure it, to link of course on which environment you need to do your automate testing and. also the Azure DevOps project in which you have setup all test cases.

![RSAT-1.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587419964576-CQCUASBUNABMHFCXADJB/ke17ZwdGBToddI8pDm48kIP_GdbA7XrSuQjbiSF68z1Zw-zPPgdn4jUwVcJE1ZvWEtT5uBSRWt4vQZAgTJucoTqqXjS3CfNDSuuf31e0tVGsZfKXdzgqR6l_s6sG3TyHzp8cISuhYtjr4xTqK-6DEJuG45vQwBxdpDrCGUSSl5w/RSAT-1.PNG)

![RSAT-1.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-1.PNG)

Change your Azure DevOps URL, put your Personal Access Token generated before, you will now use your Project and the Test Plan created in Azure DevOps before.

![RSAT-2.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587420106026-9F6LQXFYYSC25FTRZ9B5/ke17ZwdGBToddI8pDm48kBJd1flZ3m1dQkVCCqJKfjAUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKch4LLz7u81zdvJV_LiVZOngehF5Nk0RkMwCKm4dF55fkBIjY3jbOcBwB5OiMSAIdT/RSAT-2.PNG)

![RSAT-2.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-2.PNG)

After it’s a little more complicated.

Put your hostname, the URL of your FinOps instance without HTTPS.

For the SOAP Hostname, it’s the same but with **aossoap** between the firstpart of your hostname and .sandbox part. Like me below :

![RSAT-3.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587420244950-PIZFJ1GFU5UW43UZHNTE/ke17ZwdGBToddI8pDm48kNMig4wQ14-IHOXb0vj8KNBZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIMDA8O-NWvBnQOfngVy4LWiCM7ISZCy7S-oWj2oZJ4VU/RSAT-3.PNG)

![RSAT-3.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-3.PNG)

Put your admin user name email address. The account that will launch every test in your instance. And the company name / legal entity. Keep in mind that we can change on every test the legal entity in a parameter file.

For the Thumbprint, it’s a certificate to generate on your computer. Click on new. Copy the number. You will need a technical guy to put it in the environment where you plan to execute the TEST.

In the wif.config located in K:\\AosService\\WebRoot

\-> Don’t forget to apply on each AOS Server !

    <authority name="CN=127.0.0.1">
        <keys>
            <add thumbprint="xxxxxxxxxxxxxxxxxxxxxxxxx" />
        </keys>
            <validIssuers>
                <add name="CN=127.0.0.1" />
            </validIssuers>
    </authority>    

Save a Working Directory folder and also Default Browser to Google Chrome. Don’t forget after configuration to click on Save As, in order to save the configuration and maybe share it for other consultants in your project.  
Load your test plan and click after on Generate Test Execution and Parameters files.

![RSAT-3.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421233339-IRKXAM2K3W7X09MXJKMC/ke17ZwdGBToddI8pDm48kNtJohiWGu7qrUanJCxUpYZZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIeuzTwte2uipQtPAih4BjH6_M-Z3F3RqeHjMkFWd9EiU/RSAT-3.PNG)

![RSAT-3.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-3(1).PNG)

![You need to have all your test cases here and now you can check on each the Parameter files and change everything needed.](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421269952-QVQKA63YBCJVHJGNEATL/ke17ZwdGBToddI8pDm48kOxKQu92YIRb02hJUxhveKl7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1USNu5lvd_fY4DFceG4CBLYVJeCQAOqiS8zrm1SYAHE_AP7cJNZlDXbgJNE9ef52e8w/RSAT-4.PNG)

![You need to have all your test cases here and now you can check on each the Parameter files and change everything needed.](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-4.PNG)

You need to have all your test cases here and now you can check on each the Parameter files and change everything needed.

![RSAT-5.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421400995-VWFUEHBAXSBIOOF0UTFG/ke17ZwdGBToddI8pDm48kD55Xg60Iu-qPz8TdaTIRUB7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1USgBFiv-QW-Nt70QPJXHexQZsyvJ68ce6dAy_t0Pe9iIJvwGh1qtNWvMhYKnvaKhbA/RSAT-5.PNG)

![RSAT-6.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421400651-P801WOJ630ABZSBW3SY4/ke17ZwdGBToddI8pDm48kPzbhu-KzV3RwwFKEqQMr9R7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTm-T5H2ObAtnfa5PvPlCic1Jpc4qhP3KsacZQxrbiLNAw_g4qaUA-1atQkAMlxGoPA/RSAT-6.PNG)

![RSAT-7.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421401567-HUTX1OVCKINV3QLWFX4N/ke17ZwdGBToddI8pDm48kKgNBWVuV9waZQMfx3ebUGR7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTm8xZ7GFA6Utp-YIo88jNkLXzbep55TkNgZZCfKdF06F0Rz4vu439378Eh1ipxiN8m/RSAT-7.PNG)

![RSAT-5.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-5.PNG)![RSAT-6.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-6.PNG)![RSAT-7.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-7.PNG)

You can pick one, like the Customer RSAT process. And click on “RUN” ; before a small warning , click on YES.

![RSAT-8.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421449318-GG1YEKLT0F0O3Q0V1GU4/ke17ZwdGBToddI8pDm48kNnmJjoqWSPQsycqP5SdAB5Zw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PI4eEyyNZKSX8GcS5-Itj27MpG9nwaNzjGjkk4xf1Nro8/RSAT-8.PNG)

![RSAT-8.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-8.PNG)

Normally, Google Chrome will launch automatically and test everything without any actions on your hand !!

So maybe a good way is to install RSAT on a network server machine or a VM in Azure and launch every tests on night, without to keep your local machine / PC turned on : because it can takes some time, depending on the amount of test you have.

At the end, you can see the result directly in RSAT on each of them.

But the best way is after to upload the result directly on Azure DevOps. So that everyone, especially Technical Guys can get the log files in there if they are some errors.

For that, click on Upload part and “all modified automation files”

![RSAT-9.PNG](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421725744-U4F5O9MHM1RCQ719QEG1/ke17ZwdGBToddI8pDm48kCilVmaBqfdwMN8gxdFX4m5Zw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIs_Q2Hu2Fi0tIdmbSDzWmzztGkT-UJu7SIF2-7TQ1vGI/RSAT-9.PNG)

![RSAT-9.PNG](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/RSAT-9.PNG)

Now go to your Azure DevOps Test Plan.

You can see all Recent test runs

![Screenshot 2020-04-21_00-30-44-231.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587421971652-E92NL6MVCFKNUA5NW9Y7/ke17ZwdGBToddI8pDm48kBOpWS5gX6INY-PUGzxr2FIUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYwL8IeDg6_3B-BRuF4nNrNcQkVuAT7tdErd0wQFEGFSnGZIEpBabk_LqqiIQbmBMLjD-FIRsvB4suMknYKStT68oFoAC5Ip_lNIcer8VrtvQw/Screenshot+2020-04-21_00-30-44-231.png)

![Screenshot 2020-04-21_00-30-44-231.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-21_00-30-44-231.png)

If we go to the one of RSAT that goes well.

![Screenshot 2020-04-21_00-32-06-314.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587422193157-ZLPY10FNOMWW89J35BEA/ke17ZwdGBToddI8pDm48kEM_QJaVtoFPCPYWjJwP2IB7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0oo6a4sN_893sD4JAjJ8gmwhKXUcyPrL5HrgFT4ulzQ3pshJxhrxVBFFbZw3SCeaKA/Screenshot+2020-04-21_00-32-06-314.png)

![Screenshot 2020-04-21_00-32-06-314.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-21_00-32-06-314.png)

You can have all the Log needed, tracked by RSAT.

On something that went wrong… but in unit test with SysTestFramework that we will see after, the developer can have the .AXTR file generated for her/him.  
Also we can generate automatically a Bug/Work Item based on a failed test and alert me :)

![Screenshot 2020-04-21_00-31-37-242.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587422283018-SBWKPEWR5NLUY5BKZWUW/ke17ZwdGBToddI8pDm48kEF1__AY92MCJeQymbAUHhJ7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0jPA-99fl4QCEe6PK9TQA1XbWhsbBDYRXp-kDVybjnv6MXXYDU8sdpXxqISVQy3_Cg/Screenshot+2020-04-21_00-31-37-242.png)

![Screenshot 2020-04-21_00-31-37-242.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-21_00-31-37-242.png)

For Project Manager, we have also a report that we can change if you want !

![Screenshot 2020-04-21_00-33-18-567.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587422416505-F243EX24ZVHFANU5FBNO/ke17ZwdGBToddI8pDm48kMHpjW2eaAUwoWU-AGjAYKh7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0sLG3COZnet_e0tFwRthez7oFq5PMBbUYQIypw0sbdQllYiesJ7e-zzBF5z7joF09A/Screenshot+2020-04-21_00-33-18-567.png)

![Screenshot 2020-04-21_00-33-18-567.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-21_00-33-18-567.png)

  
To conclude RSAT, yes we can deeper. I don’t have setup this kind of thing in this example, but you can of course :

*   Create derivated test case : The Regression suite automation tool (RSAT) lets you to use the same task recording with multiple test cases, so that you can run a task with different data configurations. To do this, select a test case in the Regression suite automation tool and then select **New > Create Derived Test Case**. This creates a child test case in Azure DevOps. The resulting derived test case is linked to its parent test case in Azure DevOps. It has an Excel parameters file attached but no recording file. The derived test case will appear in the Regression suite automation tool grid under the same test suite with the **Derived** column selected. Derived test cases are named after their parent test case with a numeric suffix.
    
*   Chaining : One of the key features of the Regression Suite Automation Tool is the chaining of test cases, that is, the ability of a test to pass values to other tests. Test cases are executed according to their defined order in the Azure DevOps test plan, which can also be updated in the test tool itself. It is important to correctly order the tests if you want to pass variables from one test case to the other.
    
    To save the value of a variable while recording the test in Task Recorder, right-click the field and select **Task recorder > Copy**, as shown below. This will save the variable in the recording file. This variable can be used in subsequent tests.
    
*   Validate expected values : An important component of a test case is validation of expected values. You can define validation parameters during the authoring of your test cases using Task Recorder. While recording, right-click on a control and select **CurrentValue** under the **Task Recorder > Validate** menu. This action becomes a validation step that you can use with the Regression suite automation tool. The control value will become a validation variable in the automatically generated Excel parameters file. The menu item is shown in the following image.
    

![When RSAT generates the Excel parameter file for a test case, validation steps are added as shown in the image below. You can enter the expected value to use during execution of the test case.](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587422647772-YNSLU1Y289ZQ90RH6IHP/ke17ZwdGBToddI8pDm48kKJ0b6qf28Ar7saf6glsNddZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpwQVHs1bt435D36HnxtOpzJmlitV8IfkpXgQTe4sgyk3FssXVwl5tlbct2h4goZrjI/validate-test-case.png)

![When RSAT generates the Excel parameter file for a test case, validation steps are added as shown in the image below. You can enter the expected value to use during execution of the test case.](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/validate-test-case.png)

When RSAT generates the Excel parameter file for a test case, validation steps are added as shown in the image below. You can enter the expected value to use during execution of the test case.

![rsat-validate-variables.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587422691431-TG82USFR6QG5G08NK9XW/ke17ZwdGBToddI8pDm48kOdlrrKiscqQdW1VaxnwY4RZw-zPPgdn4jUwVcJE1ZvWEtT5uBSRWt4vQZAgTJucoTqqXjS3CfNDSuuf31e0tVFYkVxfQemEU_p1ZD1YKKQ49Bimi2EHDYkMvY4ZZEoCsaEcAfnVBrEqrgp1UxUHGkY/rsat-validate-variables.png)

![rsat-validate-variables.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/rsat-validate-variables.png)

[Learn more RSAT](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/perf-test/rsat/rsat-validate-expected?WT.mc_id=BA-MVP-5003744)

* * *

**3/ SysTestFramework and ATL : Acceptance test library**
---------------------------------------------------------

![54.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587414152757-21MAX75GVHI5LJD78FT5/ke17ZwdGBToddI8pDm48kLROaCskEsHFnCsBUuA_tlFZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PI1w5ynUc_FMWV9hJxizWJvxDFsMrOBfl-eUoWmXIOIBw/54.png)

![54.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/54.png)

**Key concepts**
----------------

*   Use SysTest Framework to author unit/component test code.
    
*   Test isolation
    
*   Test module creation to manage test code and FormAdaptors.
    
*   Import Task Recorder recordings into Visual Studio to generate test code.
    
*   Overview of the ATL framework
    
*   Integrate a Test module with a build machine.
    

Clearly this part is mostly for developers, but I think it’s useful also for project manager or functional consultant to know which unit test in the code can be done, despite the RSAT tool that we see before. In fact, all task recorders can be a good start to include it in the Development machine to generate simple test case, but I will show also other Framework that developers can use to be more confident before pushing a new development in a TEST environment…

So, first best practice, before going to start writing tests, you will need a new model for that ! To achieve that, go to your Visual Studio and create a new one like me :

![Screenshot 2020-04-20_22-43-39-536.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587415637068-0FKRB4HWSFR2L8WV123E/ke17ZwdGBToddI8pDm48kDEcEnbyLA63zdjdlV1fdokUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcw0F4rPp0H3WBw8PffFab6eITZYfiXLCtMD192KCnEYx-fI40WFTZQ4_0HHLRF9zR/Screenshot+2020-04-20_22-43-39-536.png)

![Screenshot 2020-04-20_22-43-39-536.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-43-39-536.png)

Important after, select your reference package. Like me, I select my main package of custom codes - solution to TEST

![Screenshot 2020-04-20_22-44-11-534.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587415731082-TL1NV2T3J1D80PB4BNZY/ke17ZwdGBToddI8pDm48kCYvPX0Z5HAmhqlhNGaEIRkUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcuGfH6zwfSQOmgE4fsamHBSnpVCihkmQDDa-g930GyqND8AcEA0Ug7TzJGB4MX3Bo/Screenshot+2020-04-20_22-44-11-534.png)

![Screenshot 2020-04-20_22-44-11-534.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-44-11-534.png)

Check : Create new project, it will here where you will place all your unit tests - and don’t of course make it as the default model for new projects.

![Screenshot 2020-04-20_22-44-49-881.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587415783679-TFGGTB0X2OZJ8RSWGZXD/ke17ZwdGBToddI8pDm48kHsGnMx4wT-y1fCT6VpdvsYUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcM1696AgC5JGPlezsD-0FIRtTJo9h-uRmIJJOcAmTkD7ymgKIWojEpYXRHqCPHbAP/Screenshot+2020-04-20_22-44-49-881.png)

![Screenshot 2020-04-20_22-44-49-881.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-44-49-881.png)

After the model is created, change the reference packages, to include **ALL FormsAdaptators models,** and the main one : **Test essentials model** !

Also put your main VS project as the Startup object !

![Screenshot 2020-04-20_22-46-10-271.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587415884450-9P1NVH04KJN3IGLC4DGS/ke17ZwdGBToddI8pDm48kNwm83ZvMUFfcWryQH8O4okUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYy7Mythp_T-mtop-vrsUOmeInPi9iDjx9w8K4ZfjXt2dp8eNt11yG2YtmampLwmCKqR3IRkeUwhvUVwGHkr6ZJ7CjLISwBs8eEdxAxTptZAUg/Screenshot+2020-04-20_22-46-10-271.png)

![Screenshot 2020-04-20_22-46-10-271.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-46-10-271.png)

So now, you have your TEST model, all referenced packages included, include your main one of course. You can now built custom test unit code with SysTestFramework;

On my side, I will use the Task Recorder Add-in in VS, but of course, **you can built your own one without addin**. Especially to test custom method on a custom class etc…

To generate test class automatically, click on Addins / Import Task Recording

![Screenshot 2020-04-20_22-56-02-444.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587416180025-HVOEWOWL5AQYHRA63STO/ke17ZwdGBToddI8pDm48kBsHSxqOcMHU36uZNb3JfKd7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UV1R-WiEHlb1fRP-OXotl3Usiey8TgDH-txyey2gZ1Xv7zs2yPjc1ECvpa5Zm_kMqw/Screenshot+2020-04-20_22-56-02-444.png)

![Screenshot 2020-04-20_22-56-02-444.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_22-56-02-444.png)

Import the file that you have in the AzureDevOps Unit Test case that we see before (like me : Recording.xml) : as you can see BPM / LCS and Azure DevOps is not only for RSAT !  
Select of course your new model for testing purpose.

![Screenshot 2020-04-20_23-01-07-796.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587416505894-41HOE52LNY9L8HDYRRNX/ke17ZwdGBToddI8pDm48kMrk5qpoWAbmrXnx9KGXZv9Zw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIP5Y1JdatR6plaiEEYNtHAownhAbfAgRj6nP0jeiOiH4/Screenshot+2020-04-20_23-01-07-796.png)

![Screenshot 2020-04-20_23-01-07-796.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-01-07-796.png)

You will have a new generate class, like me

Change just the top of the class, like adding a SysTestCategory, it will be helpful by doing some filters in the BUILD pipeline of Azure DevOps. Select the legal entity where you want to achieve your test and also add an AutoRollback statement in order to erase all your data after testing process.

This class is of course simulate each UI testing, as well as data / business process testing.

![Screenshot 2020-04-20_23-03-17-041.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587416720490-2MFEDKA715XWARGWIP6Z/ke17ZwdGBToddI8pDm48kArbkFmpIj5INRey4wl7TJx7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UY5pxqYqUilPr908wrOgSL3JzsUfE7ALBafa0wo7CCOS7zs2yPjc1ECvpa5Zm_kMqw/Screenshot+2020-04-20_23-03-17-041.png)

![Screenshot 2020-04-20_23-03-17-041.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-03-17-041.png)

You can of course directly after run & check if your TEST is OK in your DEV environment by going in the Test Explorer view.

![Screenshot 2020-04-20_23-04-06-977.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587416829150-NYB853R1JTDT0K1GU1MX/ke17ZwdGBToddI8pDm48kM1O1CUFCN78Q8dkdco4rQpZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpxOyK1luL83U4IILw0TigbmJi-9hxum1xAdPsllQ_7lHCJXf2ah7drENBuKMhd9Kc0/Screenshot+2020-04-20_23-04-06-977.png)

![Screenshot 2020-04-20_23-04-06-977.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-04-06-977.png)

This test can be now included in your version control to be used as the BUILD pipeline step in order to automatically runs every night all your SysTest Framework test.

[Learn more on SysTest Framework](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/perf-test/systest-filtering?WT.mc_id=BA-MVP-5003744)

Since 10.0.2 I think, we have now a new Test Framework in order to achieve more testing class, as far as I seen it’s really much better & easier than SysTestFramework.

The Acceptance test library (ATL) is an X++ test library that offers the following benefits:

*   It lets you create consistent test data.
    
*   It increases the readability of test code.
    
*   It provides improved discoverability of the methods that are used to create test data.
    
*   It hides the complexity of setting up prerequisites.
    
*   It supports high performance of test cases.
    

Since I don’t want to rewrite all the Microsoft documentation and since it’s very well good documented, I highly recommend to have a look on it for developer.

[Learn more on ATL Framework](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/perf-test/acceptance-test-library?WT.mc_id=BA-MVP-5003744)

* * *

**4/ Change your BUILD & Release pipeline in AzureDevOps**
----------------------------------------------------------

  
Now go to your AzureDevOps project, on the BUILD pipeline part. We will change the pipeline to include automate testing from SysTest Framework or ATL that we saw before.

On my side, I’ve got a BUILD Main, only one. So of course, change & adapt on your needs. Enable the 3 tasks at the end : **Test Setup, Execute and End Tests.**

![Screenshot 2020-04-20_23-22-37-415.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587418028427-GS8XFJQANVGY4B2FNZ70/ke17ZwdGBToddI8pDm48kPV_qnE4tBRcCSnqtN8uGiF7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTma7zep8-VRTmjYiDphuiSo0fEbKDTcNAUJYIGgLV0blOn_xbeuD5YHNveO2_f6HK1/Screenshot+2020-04-20_23-22-37-415.png)

![Screenshot 2020-04-20_23-22-37-415.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-22-37-415.png)

For these 3 steps, don’t need to change large setup, Just use Task version 2.\* and the only change is on the Variable part, to include the SysTestCategory that I had before, remember :) also added my main model.dll that host all my test class.

![Screenshot 2020-04-20_23-22-51-535.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587418181700-9XIESISVHVHGV63MQCMA/ke17ZwdGBToddI8pDm48kFFB8yMcgIBeD7EpD7jfT9t7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UX2vc1I2UAy32OiM19p40Azc4errXLDjDyxKZ5hzo9EOZtJ3qR9G2BYeA0wOAaeYNg/Screenshot+2020-04-20_23-22-51-535.png)

![Screenshot 2020-04-20_23-23-24-739.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587418182136-UAZO8NUGFSEZEMNSNHKD/ke17ZwdGBToddI8pDm48kCByG4_B6_-8fJ31ZyGXi017gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UaBIzwzWq-r6fr7zEKELrq1qNGe3yJ8TK2Q6PJMW2FWf3WUfc_ZsVm9Mi1E6FasEnQ/Screenshot+2020-04-20_23-23-24-739.png)

![Screenshot 2020-04-20_23-23-38-414.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587418183181-VLMND01BKO8QOTAUBNZZ/ke17ZwdGBToddI8pDm48kNd9z0MwgN8XfZNHY_d9Al97gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1Ue74T3P0FDZe6h_h-Q9EBTJmZ2jSPYxiyQqaQb5p-WKhbSexTd1-frD7527z4SM9QQ/Screenshot+2020-04-20_23-23-38-414.png)

![Screenshot 2020-04-20_23-24-24-388.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1587418184040-P78JXCIEFTK164SEVWLF/ke17ZwdGBToddI8pDm48kJ_CmNmnnvQECgcR2OT1mrx7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0kwsEKcmM5zBs3WNR9NCFsCcIIOt6Wb6IL8JIFI1IXU2g_mJvftJio5T9j_2hsiTHA/Screenshot+2020-04-20_23-24-24-388.png)

![Screenshot 2020-04-20_23-22-51-535.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-22-51-535.png)![Screenshot 2020-04-20_23-23-24-739.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-23-24-739.png)![Screenshot 2020-04-20_23-23-38-414.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-23-38-414.png)![Screenshot 2020-04-20_23-24-24-388.png](./Automate your tests for Dynamics 365 Finance and Operations — PowerAzure365_files/Screenshot+2020-04-20_23-24-24-388.png)

  
So here we are you are a Pro of Automate testing in Dynamics 365 Finance and Operations.

To conclude, we can also use PowerAutomate with AzureDevops : doing a morning a test plan report email ? Also create an adaptive card for deployment approval on release pipeline after seeing the test report ?

Well as you can see, you have now all in your hands features to achieve a lot automate testing.