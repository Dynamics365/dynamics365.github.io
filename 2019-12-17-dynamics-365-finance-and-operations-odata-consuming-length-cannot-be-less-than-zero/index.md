# Dynamics 365 finance and operations ODATA consuming - Length cannot be less than zero

When you consume a custom data entity, you get an error

{{< admonition failure >}}
errorSystem.ArgumentOutOfRangeException : Length cannot be less than zero” and it works fine for standard data entities.
{{< /admonition >}}

![Image](Dynamics-365-finance-and-operations-ODATA-consuming-Length-cannot-be-less-than-zero_1.png)

The reason is the temporary XML file where the metadata stored which mismatches with the metadata from `https://<yourenvironment>.cloudax.dynamics.com/data/$metadata`

You can follow this [post](/2019-12-16-consuming-dynamics-365-finance-and-operations-odata-services-from-net/) to understand how to create the XML file.

If you try to regenerate the metadata by saving the **ODataClient.tt** file, the XML will be accumulated, and the error keeps happening. 
The resolution here is simply delete it and regenerate metadata again by saving the **ODataClient.tt** file

![Image](Dynamics-365-finance-and-operations-ODATA-consuming-Length-cannot-be-less-than-zero_2.png)

Thank you for reading.

