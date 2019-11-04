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

### 2. Application registration int AAD

We need an application to authenticate with FinOps and Azure function HTTPs. 
Go to *Azure portal > AAD > App registrations > New registration*

<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_2.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_2.png" alt=""></a>
</figure>

  * Name of the application.
  * Depend on you have multitenant or not
  * Dynamics 365 finance and operations URL

  ## a. API Permissions

Go to the newly created application > API permissions and adding permission as below
<figure class='center'>
  <a href="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_3.png"><img src="{{site.url}}/assets/imagesposts/2019-11-05-Business-events-and-HTTPs-endpoint_3.png" alt=""></a>
</figure>



Thank you for reading.
