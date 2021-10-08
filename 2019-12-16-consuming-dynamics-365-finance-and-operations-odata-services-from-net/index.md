# Consuming Dynamics 365 Finance and Operations OData services from .NET


# 1. Authentication 

We need an authentication to connect Dynamics 365 finance and operations from .Net application by using service principal.  

How to create an app registration in Azure [https://docs.microsoft.com/en-us/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) 

You also need to add required permissions for Dynamics 365 finance and operations, please follow  
[https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/services-home-page#register-a-web-application-with-aad](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/services-home-page#register-a-web-application-with-aad)

This is what you should have

![Image](/imagesposts/Consuming-Dynamics-365-Finance-and-Operations-OData-services-from-NET_1.png)

# 2. Register your external application

### a. In the FinOps application, go to **System administration > Setup > Azure Active Directory applications**.
### b.	Select **New**.
### c.	Fill in the fields for the new record:

* In the **Client Id** field, enter the application ID that you registered in Azure AD.
* In the **Name field**, enter a name for the application.
* In the **User ID field**, select an appropriate service account user ID. For this example, we have selected the Admin user. However, as a better practice, you should provision a dedicated service account that has the correct permissions for the operations that must be performed. When you've finished, select **Save**.

You've now finished setting up the prerequisites. After the external application retrieves an Azure AD authentication token, it should now be able to use the token in an authorization HTTP header to make subsequent service calls via OData or SOAP, for example.

# 3. OData client configuration

* Download the project here https://github.com/microsoft/Dynamics-AX-Integration
* Open **ServiceSamples** solution.
* We cannot use existing proxies and classes, so I need to regenerate them again. Under ODataUtility project, delete **ODataClient.tt** and **ODataClient.ttinclude**. 
* To regenerate Odata client, right click on *ODataUtility project > Add > New item*, search for OData in Online and rename it to **ODataClient.tt**.
* Open **ODataClient.tt**, in MetadataDocumentUri add *"https://<yourEnvironment>.cloudax.dynamics.com/data/$metadata"*

If you save the ODataClient.tt file, it will generate Odata proxies and classes. From PU12 onward, there are so many entities so you will get an error after compiling 

`Combined length of user strings used by the program exceeds allowed limit. Try to decrease use of string literals.`

There are 2 ways to fix it

### a. Using temporary file

The simplest way to fix is add TempFilePath, ensure that you have write permission for this path, this is what you got

![Image](/imagesposts/Consuming-Dynamics-365-Finance-and-Operations-OData-services-from-NET_2.png)

Save the **ODataClient.tt** and there will be **Test1.xml** file created to store metadata, there should be a bug while generating the xml file, one more step you need to do is replace the double quote to single quote (a global replace of "" with " ), and this is what you have 

![Image](/imagesposts/Consuming-Dynamics-365-Finance-and-Operations-OData-services-from-NET_3.png)
You can build the project without problem.

### b. Remove unused entities

Another work-around that works too, edit the ODataClient.ttinclude so that it parses only some of the entities that you want to use, this reducing the error. For instance, change the following foreach statement

* Original code:

```c#
foreach (IEdmEntitySet entitySet in container.EntitySets())
{
    IEdmEntityType entitySetElementType = entitySet.EntityType();
    string entitySetElementTypeName = GetElementTypeName(entitySetElementType, container);

    string camelCaseEntitySetName = entitySet.Name;
    if (this.context.EnableNamingAlias)
    {
        camelCaseEntitySetName = Customization.CustomizeNaming(camelCaseEntitySetName);
    }
}
```

* Modified code:

```c#
foreach (IEdmEntitySet entitySet in container.EntitySets())
{
    IEdmEntityType entitySetElementType = entitySet.EntityType();
    string entitySetElementTypeName = GetElementTypeName(entitySetElementType, container);

    string camelCaseEntitySetName = entitySet.Name;

    //start of manual fix 
    //only process entity names that containin a specific string, to reduce the string size
    if (((camelCaseEntitySetName.Contains("CUST")) || (camelCaseEntitySetName.Contains("VEND"))) || (camelCaseEntitySetName.Contains("SALES")))
    {
      // emd of manual fix
      if (this.context.EnableNamingAlias)
      {
          camelCaseEntitySetName = Customization.CustomizeNaming(camelCaseEntitySetName);
      }
    }
}
```

# 4. Authentication configuration

Under AuthenticationUtility project, you need to modify ClientConfiguration.cs follow, you can get all related information for the first step.

![Image](/imagesposts/Consuming-Dynamics-365-Finance-and-Operations-OData-services-from-NET_4.png)
 
From now, you can freely test the integrations under **ODataConsoleApplication** project.
If you are testing with a custom data entity and you get this error 

`System.ArgumentOutOfRangeException : Length cannot be less than zero.`

Please check this [post](https://nuxulu.com/2019/12/17/Dynamics-365-finance-and-operations-ODATA-consuming-Length-cannot-be-less-than-zero.html).

Thank you for reading.

