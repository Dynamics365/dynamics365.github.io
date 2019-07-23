---

title: Handle AIF error messages in dynamics AX 2012 R3
description: Handle AIF error messages in dynamics AX 2012 R3
author: Max Nguyen
modified: 2017-01-12
categories: [ax2012, trick, tools, integration]
tags: [xpp, aif]
key: d365
---

Normally, when we consume AIF Service, we use this code like below to handle Error messages

{% highlight csharp %}
try
{
	client.register(ctx, contract);
	Console.WriteLine("items registed on Trans Id: " + contract.InventTransId + " with " + contract.Qty + " quantities.");
	Console.ReadLine();
}
catch (Exception ex)
{
	Console.WriteLine(string.Format("Ex: {0}", ex.Message));
	Console.ReadLine();
}
{% endhighlight %}

If it cause error, message would return like this

![]({{site.url}}/assets/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-1.png)

<!--more-->

If you want to know more details, you have to go In Dynamics ax **AIF Exceptions form** then check

![]({{site.url}}/assets/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-2.png)

It's quite hard for 3rd party developer, especially they don't have right to access AX server.

Anyway, we can get meaningful error message by doing below steps

* Check that box in AIF inbound ports

![]({{site.url}}/assets/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-3.png)

* Use `FaultException` class to get message

{% highlight csharp %}
try
{
	client.register(ctx, contract);
	Console.WriteLine("items registed on Trans Id: " + contract.InventTransId + " with " + contract.Qty + " quantities.");
	Console.ReadLine();

}
catch (System.ServiceModel.FaultException<ItemsRegistration.RegRef.AifFault> aifFault)
{
	//FaultMessageList[] list = aifFault.Detail.FaultMessageListArray[0];
	InfologMessage[] list = aifFault.Detail.InfologMessageList;

	foreach (InfologMessage message in list)
	{
		Console.WriteLine(message.Message);

	}
	Console.ReadLine();

}
{% endhighlight %}

what we got

![]({{site.url}}/assets/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-4.png)

Thank you for reading.
