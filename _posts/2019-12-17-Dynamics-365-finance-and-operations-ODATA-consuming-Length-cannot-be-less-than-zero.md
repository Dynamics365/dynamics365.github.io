---
date: 2019-12-17 00:00:00+00:00
title: Dynamics 365 finance and operations ODATA consuming - Length cannot be less than zero
tags:
- Length cannot be less than zero
- OData
- BATCH
- integration
- .NET
author: Max Nguyen
key: Dynamics-365-finance-and-operations-ODATA-consuming-Length-cannot-be-less-than-zero
cover: /assets/images/test2.jpg
---


When you consume a custom data entity, you get an error

errorSystem.ArgumentOutOfRangeException : Length cannot be less than zero” and it works fine for standard data entities.
{:.error}

![Image]({{site.url}}/assets/imagesposts/Dynamics-365-finance-and-operations-ODATA-consuming-Length-cannot-be-less-than-zero_1.png){:.border}

The reason is the temporary XML file where the metadata stored which mismatches with the metadata from `https://<yourenvironment>.cloudax.dynamics.com/data/$metadata`

You can follow this [post](https://nuxulu.com/2019/12/16/Consuming-Dynamics-365-Finance-and-Operations-OData-services-from-NET.html) to understand how to create the XML file.

If you try to regenerate the metadata by saving the **ODataClient.tt** file, the XML will be accumulated, and the error keeps happening. 
The resolution here is simply delete it and regenerate metadata again by saving the **ODataClient.tt** file

![Image]({{site.url}}/assets/imagesposts/Dynamics-365-finance-and-operations-ODATA-consuming-Length-cannot-be-less-than-zero_2.png){:.border}

Thank you for reading.