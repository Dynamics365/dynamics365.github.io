---
date: 2019-11-05 00:00:00+00:00
title: Business events and HTTPs endpoint
tags:
- Postman
- Business events
- BATCH
- integration
- Azure
author: Max Nguyen
key: 2019-11-05-Business-events-and-HTTPs-endpoint
cover: /assets/images/test2.jpg
---

In Dynamics 365 finance and operations, Business events provide a mechanism that lets external systems receive notifications from FinOps applications. In this way, the systems can perform business actions in response to business events.

There are 3 types of business events: [Application business events](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/business-events/app-business-events), [Workflow business events](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/business-events/business-events-workflow), and [Alerts as business events](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/business-events/alerts-business-events). You can also implement [a new business event](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/business-events/business-events-dev-doc).

Business events can be consumed using Microsoft Flow and Azure messaging services, and we use endpoint to manage the destinations for sending business events to, Microsoft supports many endpoints: Azure Service Bus Queue, Azure Service Bus Topic, Azure Event Grid, Azure Event Hub, HTTPS, Microsoft Flow.
In this article I will show how to send business event to HTTPs endpoint that leveraging on Azure functions.

The scenario: Once a free text invoice is posted, Business event will be triggered and send messages to the HTTPs endpoint.

### 1. HTTPs and Azure function

Ideally, I will create a new Azure function which has HTTPs endpoint to subscribe the business events in FinOps.
To create Azure function please follow this https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function

Here is the simple line of code

{% highlight csharp %}
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;
public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
log.LogInformation("Dynamics 365 finance and operations notifications");
//log.LogInformation(req.Query["name"]);
string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
log.LogInformation(requestBody);
return (ActionResult)new OkObjectResult($"Hello world");
}
{% endhighlight %}

This basically listens the JSON messages from Business events and display the Json message at Console.

From the Azure function you can get the HTTPs endpoint, save it for later reference.

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_1.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_1.png" alt=""></a>
</figure>

## 2. Application registration int AAD

We need an application to authenticate with FinOps and Azure function HTTPs. 
Go to *Azure portal > AAD > App registrations > New registration*

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_2.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_2.png" alt=""></a>
</figure>

  * Name of the application.
  * Depend on you have multitenant or not
  * Dynamics 365 finance and operations URL

  ### API Permissions

Go to the newly created application > API permissions and adding permission as below
<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_3.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_3.png" alt=""></a>
</figure>

### Secrets

Go to Certificates & secrets menu item and create a new client secret

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_4.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_4.png" alt=""></a>
</figure>

After this you will have **Application Id and Application Secret**, save it for later.

## 3. Key Vaults

In Azure portal create a new keyVault to store the HTTPs endpoint URL information

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_5.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_5.png" alt=""></a>
</figure>

### Access policy

Click next to create access policy (you also can set up this later after creating Key Vault)

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_6.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_6.png" alt=""></a>
</figure>

Select all the permissions in Key, Secret and Certificate, In select principal choose the application you have created before.

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_7.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_7.png" alt=""></a>
</figure>

### Key Vault secret

Go to the newly *created Key vault > secrets > generate a new one*

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_8.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_8.png" alt=""></a>
</figure>

Value is the endpoint URL for D365 to call the one we got from the first step.
After this step you will have the Key vault DNS name *https://maxfokeyvault.vault.azure.net/* and Key Vault secret name *D365VaultSecretName*
That information will be needed for Business events configuration in Dynamics 365 finance and operations 

## 4. Creating HTTPs endpoint

Go to *System administrator > Business events > Business events catalog*, Click on **Endpoints** in **Endpoint type** choose *HTTPS* and **click Next**. Fill all the required information that you got from above steps.

Click on **Business events catalog**, look for business event Id *CustFreeTextInvoicePostedBusinessEvent*, check the record and click **Active**; from there choose legal entity and the Endpoint that we have just created.

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_9.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_9.png" alt=""></a>
</figure>

After that, if you check on **Active events tab**, there will be a new record created.
That's it, now I will create a free text invoice and post it, this is what I got from the console log in Azure.

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_10.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_10.png" alt=""></a>
</figure>

With the JSON messages, you can deserialize it and save to Cosmos DB or do whatever in Azure function.

## 5. About platform changes

Before PU26, Business event run in batch, following menu *System admin > Business events > Start business events batch job*

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_11.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_11.png" alt=""></a>
</figure>

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_12.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_12.png" alt=""></a>
</figure>

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_13.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_13.png" alt=""></a>
</figure>

In **BusinessEventsParameters**, the value will be ```Enabled = 0 , BatchEnabled = 1```. 

After PU26, the sending business events will be triggered directly from FinOps, you won't find the menu System admin > Business events > Start business events batch job

In **BusinessEventsParameters**, the value will be ```Enabled = 1 , BatchEnabled = 0```.
If you have just upgraded from PU26 to higher version, the Business events are not getting triggered because the value might not be changed in **BusinessEventsParameters** and there is no batch. You should check this table according to the situation.

Thank you for reading.
