# Insert multiple records in single request using Odata from Postman


Got inspired by [this topic](https://community.dynamics.com/365/financeandoperations/f/dynamics-365-for-finance-and-operations-forum/286810/inserting-multiple-records-via-odata-from-postman), I'd like to write this article to show you how to post multiple records in single request by using Postman.

Generally, batch requests are supported in the [OData service](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398359), The easiest way is you can use the C# code approach from [github](https://github.com/Microsoft/Dynamics-AX-Integration/tree/master/ServiceSamples/ODataConsoleApplication) and the excel add-ins in Dynamics 365 for finance and operations use Odata batch to communicate in a single request but how can we leverage it in Postman.

Using Excel add-in then add 2 customer groups records and submit to Dynamics 365 for finance and operations, while you are doing that using [fiddler](https://www.telerik.com/fiddler) on the same box to see how the odata batch framework works.
This can be done from POSTMAN too and you need to use header and body as you see in Fiddler.

For basic setting up Dynamics 365 for finance and operations and Postman please follow [this offical document](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/data-entities/third-party-service-test)

### 1. Get Dynamics 365 for finance and operations authorization

This is a result
<figure class='center'>
  <a href="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_1.png"><img src="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_1.png" alt=""></a>
</figure>

### 2. Create a new POST request in Postman with header

<figure class='center'>
  <a href="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_2.png"><img src="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_2.png" alt=""></a>
</figure>

***URL: {{resource}}/data/$batch***
***Content-Type: multipart/mixed;boundary=batch_d63a-e9be-2927***

### 3. Modify the Body in Postman

I will place 2 records for customer groups

<figure class='center'>
  <a href="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_3.png"><img src="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_3.png" alt=""></a>
</figure>

Full text here

```cmd
--batch_d63a-e9be-2927
Content-Type: multipart/mixed; boundary=changeset_2499-90ab-7b93

--changeset_2499-90ab-7b93
Content-Type: application/http
Content-Transfer-Encoding: binary

POST CustomerGroups?cross-company=true HTTP/1.1
Content-ID: 1
Accept: application/json;q=0.9, */*;q=0.1
OData-Version: 4.0
Content-Type: application/json
OData-MaxVersion: 4.0

{"CustomerGroupId":"1060","Description":"Wholesales customers","PaymentTermId":"Net30","IsSalesTaxIncludedInPrice":"No","dataAreaId":"usmf"}
--changeset_2499-90ab-7b93--

--batch_d63a-e9be-2927
Content-Type: multipart/mixed; boundary=changeset_b573-33b2-85ff

--changeset_b573-33b2-85ff
Content-Type: application/http
Content-Transfer-Encoding: binary

POST CustomerGroups?cross-company=true HTTP/1.1
Content-ID: 2
Accept: application/json;q=0.9, */*;q=0.1
OData-Version: 4.0
Content-Type: application/json
OData-MaxVersion: 4.0

{"CustomerGroupId":"1070","Description":"Wholesales customers1","PaymentTermId":"Net30","IsSalesTaxIncludedInPrice":"No","dataAreaId":"usmf"}
--changeset_b573-33b2-85ff--
```

Click send and this is what you got from response

<figure class='center'>
  <a href="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_4.png"><img src="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_4.png" alt=""></a>
</figure>

Full Response

```json
--batchresponse_45e87829-5a26-480e-8aaa-8a08c7a82c60
Content-Type: multipart/mixed; boundary=changesetresponse_27ed7621-d939-40b7-9f8b-be0421ff0cea
--changesetresponse_27ed7621-d939-40b7-9f8b-be0421ff0cea
Content-Type: application/http
Content-Transfer-Encoding: binary
Content-ID: 1
HTTP/1.1 201 Created
ETag: W/"JzEsNjg3MTk0Nzk4MzUn"
Location: https: //fodevb2819a3b6966913ddevaos.cloudax.dynamics.com/data/CustomerGroups(dataAreaId='usmf',CustomerGroupId='1060')
Content-Type: application/json; odata.metadata=minimal
OData-Version: 4.0
{
"@odata.context": "https://fodevb2819a3b6966913ddevaos.cloudax.dynamics.com/data/$metadata#CustomerGroups/$entity",
"@odata.etag": "W/\"JzEsNjg3MTk0Nzk4MzUn\"",
"dataAreaId": "usmf",
"CustomerGroupId": "1060",
"ClearingPeriodPaymentTermName": "",
"DefaultDimensionDisplayValue": "",
"CustomerAccountNumberSequence": "",
"IsSalesTaxIncludedInPrice": "No",
"Description": "Wholesales customers",
"WriteOffReason": "",
"PaymentTermId": "Net30",
"TaxGroupId": ""
}
--changesetresponse_27ed7621-d939-40b7-9f8b-be0421ff0cea--
--batchresponse_45e87829-5a26-480e-8aaa-8a08c7a82c60
Content-Type: multipart/mixed; boundary=changesetresponse_541a7d21-af21-4d66-b410-fb4165599b54
--changesetresponse_541a7d21-af21-4d66-b410-fb4165599b54
Content-Type: application/http
Content-Transfer-Encoding: binary
Content-ID: 2
HTTP/1.1 201 Created
ETag: W/"JzEsNjg3MTk0Nzk4MzYn"
Location: https: //fodevb2819a3b6966913ddevaos.cloudax.dynamics.com/data/CustomerGroups(dataAreaId='usmf',CustomerGroupId='1070')
Content-Type: application/json; odata.metadata=minimal
OData-Version: 4.0
{
"@odata.context": "https://fodevb2819a3b6966913ddevaos.cloudax.dynamics.com/data/$metadata#CustomerGroups/$entity",
"@odata.etag": "W/\"JzEsNjg3MTk0Nzk4MzYn\"",
"dataAreaId": "usmf",
"CustomerGroupId": "1070",
"ClearingPeriodPaymentTermName": "",
"DefaultDimensionDisplayValue": "",
"CustomerAccountNumberSequence": "",
"IsSalesTaxIncludedInPrice": "No",
"Description": "Wholesales customers1",
"WriteOffReason": "",
"PaymentTermId": "Net30",
"TaxGroupId": ""
}
--changesetresponse_541a7d21-af21-4d66-b410-fb4165599b54--
--batchresponse_45e87829-5a26-480e-8aaa-8a08c7a82c60--
```

Check the data in Dynamics 365 for finance and operations 

<figure class='center'>
  <a href="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_5.png"><img src="/imagesposts/2019-10-15-Insert-multiple-records-in-single-request-using-Odata-from-Postman_5.png" alt=""></a>
</figure>

Thank you for reading.

