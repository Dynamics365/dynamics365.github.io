# Handle AIF error messages in dynamics AX 2012 R3


Normally, when we consume AIF Service, we use this code like below to handle Error messages

```C#
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
```

If it cause error, message would return like this

![](/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-1.png)

<!--more-->

If you want to know more details, you have to go In Dynamics ax **AIF Exceptions form** then check

![](/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-2.png)

It's quite hard for 3rd party developer, especially they don't have right to access AX server.

Anyway, we can get meaningful error message by doing below steps

* Check that box in AIF inbound ports

![](/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-3.png)

* Use `FaultException` class to get message

```C#
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
```

what we got

![](/imagesposts/Handle-AIF-error-messages-in-dynamics-AX-2012-R3-4.png)

Thank you for reading.

