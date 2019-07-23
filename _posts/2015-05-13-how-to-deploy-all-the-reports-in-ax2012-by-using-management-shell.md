---

title: How to deploy all the reports in AX2012 by using Management Shell
description: How to deploy all the reports in AX2012 by using Management Shell
author: Max Nguyen
modified: 2015-05-13
categories: [ax2012, ssrs]
tags: [trick, ssrs]
key: d365
---

Login to the AX AOS server and follow below steps.

1. Click `Start > Administrative Tools`.

2. Right-click the Microsoft Dynamics AX 2012 Management Shell option.

3. Click Run as administrator.

Go to PowerShell command prompt and run with command 

`Publish-AXReport –ReportName *`

Wait up to getting Deployment successful message in command prompt.