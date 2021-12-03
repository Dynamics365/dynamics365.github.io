# How to create HTTP adapter port


We normally use `NETTCP` adapter for .NET system, It going to very easy to consume by.

As my experience, if we use `NETTCP` adapter for consuming by Java system, we will need add some extension or 3rd party due to binding and mismatch schema and of course client doesn’t want to do that either me.

So, my solution is creating HTTP adapter for Java, you can also use .NET system to consume HTTP adapter without any problems

<!--more-->

### How to do

* You need to make sure install web services component on Internet Information Services (IIS) and verify that the default website is working.

![](/imagesposts/How-to-create-HTTP-adapter-port-1.png)

* In AX, under System administrator > Services and AIF > Web sites, open from a create new record follow information

Name: `DynamicsAXAif60`
Virtual directory share path: `\\YourServerName\MicrosoftDynamicsAXAif60`
URL: `http://YourServerName/MicrosoftDynamicsAXAif60`

* From now on, you can create AIF inbound port with **HTTP adapter**.

![](/imagesposts/How-to-create-HTTP-adapter-port-2.png)

After you've actived the services, that Port will deployed under `DynamicsAXAif60` folder in IIS.

Thank you for reading.

