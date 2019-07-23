---

title: Response in AIF custom service class
description: Response in AIF custom service class
author: Max Nguyen
modified: 2017-01-12
categories: [ax2012, trick, tools, integration]
tags: [xpp, aif]
key: d365
---

At the previous [post](http://nuxulu.com/ax2012/trick/tools/integration/AIF-Custom-response-value-in-Dynamics-AX-2012-R3/) I already show how to customize Response Value list in AIF Document standard service, today we will talk about response in Custom AIF service class.

We already know for Custom AIF service we actually need 2 classes, one is `contract` for data input and one is `service` to process a logic. With `Response` class, it's literally same with `contract` class. They both hold parm value.

* **`contract` class** gets parametters.

* **`Response` class** sets return values.

### Scenario

I want to get `HcmPersonnelNumberId` and `HcmWorkerName` of current userID on C#.NET application.

<!--more-->

### Solution
	
* Write Custom AIF service to get Worker information, and then public this service.

* Write C#.NET console to consume that service.

### Walkthrough

#### Response class

{% highlight csharp %}
[DataContractAttribute]
class MaxWorkerResponse
{
    str     gId;
    str     gName;
}
{% endhighlight %}

two value that I want to return is `HcmPersonnelNumberId` and `HcmWorkerName`, I will store it in 2 parms method

{% highlight csharp %}
[DataMemberAttribute('HcmWorkerName')]
public HcmWorkerName parmName(HcmWorkerName _name = gName)
{
    gName = _name;

    return gName;
}

[DataMemberAttribute('HcmPersonnelNumberId')]
public HcmPersonnelNumberId parmId(HcmPersonnelNumberId _id = gId)
{
    gId = _id;

    return gId;
}
{% endhighlight %}

#### Service class

Create `MaxPRService` Class, This class consume through service that need to be extend `SysOperationServiceBase` class

{% highlight csharp %}
class MaxPRService extends SysOperationServiceBase
{
}
{% endhighlight %}

Main logic

{% highlight csharp %}
[SysEntryPointAttribute(true),
AifCollectionTypeAttribute('return', Types::String)]
public MaxWorkerResponse getEmployee()
{
    HcmWorkerRecId          workerRecId;
    HcmPersonnelNumberId    personnelNumber;
    HcmWorkerName           name;
    MaxWorkerResponse       response;

    workerRecId = DirPersonUser::currentWorker();
    personnelNumber = HcmWorker::find(workerRecId).PersonnelNumber;
    name = HcmWorker::find(workerRecId).name();
    
    response = new MaxWorkerResponse();
    
    response.parmId(personnelNumber);
    response.parmName(name);
    
    return response;
}
{% endhighlight %}

#### Create service

In AOT create new service and add recent created class to that Service, in operations node add `getEmployee` method, you will get something likes

![]({{site.url}}/assets/imagesposts/Respone-in-AIF-custom-service-class-1.png)

Then right click service > Add-ins > Register Service.

go to AIF inbound form to create new service and add `getEmployee` operation to that service then Active.

#### Consume service

Add recent WSDL URI `http://WINSERVER:8104/DynamicsAx/Services/MaxPurchReqGeneral` into Service reference in C# Console project

{% highlight csharp %}
static void Main(string[] args)
{
	CallContext context = new CallContext()
	{
		Company = "USMF",
		Language = "EN-US",
	};
	
	MaxPRServiceClient client = new MaxPRServiceClient();
	MaxWorkerResponse response = client.getEmployee(context);
	
	Console.WriteLine(response.HcmWorkerName + ", " + response.HcmPersonnelNumberId);
	Console.ReadLine();
}
{% endhighlight %}

Thank you for reading.

