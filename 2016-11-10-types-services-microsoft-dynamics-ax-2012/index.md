# Types of services in Microsoft Dynamics AX 2012


# Document services

Document services use documents to represent business objects such as purchase and sales orders, customers, vendors, and so on.

A document service is composed of the following components:

- **Document query** : This is a query that is created in the **Application Object Tree (AOT)** and contains all the tables that are related to the business object that you want to expose. Based on this query, the Document Service Generation Wizard can be used to generate the other artifacts that make up the document service.
- **Table AxBC classes** : An `AxBC` class is a wrapper for a table and contains business logic that is needed for **Create, Read, Update, Delete (CRUD)** operations.
- **Document class** : The purpose of the document class is to contain business logic that is associated with the creation and modification of the business entity itself. For example, the `AxdCustomer` class could contain logic to handle party information of a customer.
- **Document service class** : This is the actual service implementation class and extends the `AifDocumentService` class. This class implements the service operations that are published through the service contract.

When creating document services, developers need to make sure **that the business object is mapped correctly to the document query**. The document services framework will handle all other things such as the serialization and deserialization of XML, date effectiveness, and so on.

Document services can be deployed using the integration ports and all available adapters can be used.

<!--more-->

# Custom services

Custom services were already available in Microsoft Dynamics AX 2009, but support for **Extended Data Types(EDTs)** was limited, which resulted in developers having to provide custom serialization and deserialization logic.

Microsoft Dynamics AX 2012 introduces the concept of attributes.  **Attributes**  provide a way to specify metadata about classes and methods. Two of these attributes are used when creating data contracts: the `DataContractAttribute` and `DataMemberAttribute` attributes.

The 'DataContractAttribute' attribute is used to define that a class is a data contract. The'DataMemberAttribute' attribute is added to methods of data contracts that represent data members that have to be exposed. This way of defining data contracts is very similar to other programming languages such as C#.

Support for more complex data types such as collections and tables has been added so that these types can be serialized and deserialized without developers having to provide the logic themselves.

In a typical custom service you will find the following components:

- **Service contract** : A service contract is an X++ class that contains methods with the _SysEntryPointAttribute_ attribute. This identifies methods that will result in a service operation contract when the service is exposed.
- **Data contracts** : A data contract is an X++ class that is attributed with the  **DataContractAttribute**  attribute. It contains parameter methods that will be attributed as data members for each member variable that needs to be part of the data contract.

Custom services can be deployed using the integration ports and any available adapter can be used.

# System services

These services are new since the release of Microsoft Dynamics AX 2012. The main difference between these services and the previous two types is that they are not customizable and are not mapped to a query or X++ code. They are not customizable because they are written by Microsoft in managed code. One exception is the user session service, which is written in X++ code but is generally considered as a system service.

There are three system services available for use in Microsoft Dynamics AX 2012: the query service, the metadata service, and the user session service.


# Query service

The query service provides the means to run queries of the following three types:

- Static queries defined in the AOT.
- User-defined queries by using the _QueryMetaData_ class in the service.
- Dynamic queries that are written in X++ classes. These classes need to extend the 'AIFQueryBuilder' class.

When queries are called by a service, the AOS authorization ensures that the caller has the correct permissions to retrieve the information. This means that unpermitted fields will be omitted from the query result. Furthermore, when joined data sources are not allowed to be used, the query call will result in an error that can be caught by the calling application.

The resulting rows will be returned as an ADO.NET DataSet object. This can be very useful when you make use of controls in your application that can be bound to a DataSet object.

The query service can be found at the following address:

`net.tcp://hostname:port/DynamicsAX/Services/QueryService`


# Metadata service

This system service can be used to retrieve metadata information about the AOT. Consumers of this service can get information such as which tables, classes, forms, and menu items are available in the system. An example usage of this service could be retrieving information about the AOT and using it in a dashboard application running on the Microsoft .NET Framework.

The metadata service can be found at the following address:

`net.tcp://hostname:port/DynamicsAX/Services/MetaDataService`


# User session service

The third system service is the user session service. With this service you can retrieve information about the caller's user session. This information includes the user's default company, language, preferred calendar, time zone, and currency.

The user session service can be found at the following address:

`net.tcp://hostname:port/DynamicsAX/Services/UserSessionService`

# The right service for the right job

Now that it is clear what types of services Microsoft Dynamics AX 2012 has to offer, the question arises as to when each type of service should be used. There is no simple answer for this due to the fact that every type has its strengths and weaknesses. Let us take a look at two factors that may help you make the right decision.

# Complexity

Both document services and custom services can handle any business entity complexity. The document services framework parses the incoming XML and validates it against an **XML Schema Definition(XSD)** document. After validation, the framework calls the appropriate service action. Custom services on the other hand use the .NET XML Serializer and no validation of data is done. This means that any validations of the data in the data contract need to be written in code. Another advantage of document services over custom services is that the AxBC classes already contain a lot of the logic that is needed for CRUD operations.

# Flexibility

Document services have service contracts that are tightly coupled with the AOT Query object. This means that when the query changes, the schema also changes. Data policies allow you to control which fields are exposed. When using custom services, this cannot be done by setup, but has to be done by attributing at design time.

Custom services have the flexibility towards the service contract that the document services are lacking. Here the developer is in full control about what is in the contract and what is not. The operations, input parameters, and return types are all the responsibility of the developer.

Another benefit in using custom services is the ability to use shared data contracts as parameters for your operations. Think of a company-wide software solution that involves the use of Microsoft Dynamics AX 2012 together with SharePoint and .NET applications that are all linked through BizTalk. You could opt to share data contracts to make sure that entities are the same for all of the components in the architecture.

In that scenario, you're able to create a data contract in managed code and reference it in Microsoft Dynamics AX 2012. Then you can use that .NET data contract in your service operations as a parameter.

There will probably be more factors that you will take into consideration to choose between the service types. But we can come to the following conclusion about when to use what type of service:

- **Custom services** : Custom services should be used when exposing entities that have a low complexity or data contracts that need to be shared between other applications.

They are also ideal when custom logic needs to be exposed that may have nothing to do with data structures within Microsoft Dynamics AX.

- **Document services** : Document services should be used when exposing entities that have a high complexity and when validation of the data and structure would require a lot of work for developers to implement on their own.
- **Query service** : The query service should be used when only read operations are needed and there is no need for updates, inserts, or delete actions.

It can be used when writing .NET Framework applications that leverage the data from Microsoft Dynamics AX returned as an ADO.NET DataSet.

- **Metadata service** : Use the metadata service when metadata information about objects in the AOT is required.
- **User session service** : The user session service should be used when user session-related information is required.
