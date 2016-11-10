---
title: "Only integrated security is supported for AX queries"
layout: post
date: 2016-01-17 16:22:50.000000000 +07:00
author: Max Nguyen
categories: [services, ssrs, ax2012, trick, problem]
tags: [ssrs, programming]
---

I updated my dynamics Ax development Environment by restoring Database from **Production Database** . After the restoring the database, when I run any report form Dynamics Ax, all gave error  `“Only integrated security is supported for AX queries.”`


The reporting Services are still working fine.


For this, best way to redeploy them, create a new report folder, open Microsoft Dynamics Ax 2012 Management Shell (make sure you run Powershell as Administrator).


`Publish-AXReport -ReportName *`
