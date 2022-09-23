# Admin provisioning tools in Dynamics 365 Finance Operations


After restoring database backup, you want to be able to login to Dynamics 365 Finance Operations with different ternant, you need to use the admin provisioning tool to assign a new tenant.

<!--adsense-->

1. Before 10.0.24 verison

The admin provisioning tool can be found at

VHD: `C:\AOSService\PackagesLocalDirectory\bin\AdminUserProvisioning.exe`

Cloud- hosted: `K:\AOSService\PackagesLocalDirectory\bin\AdminUserProvisioning.exe`

After running `Provisioning.exe` wtih admin right, you will see the update for the admin user in SQL.

![Image](userinfo.png "userinfo")

Some errors you might face:

{{< admonition failure >}}
Can not stop the DynamicsAXBatch service on computer
{{< /admonition >}}

{{< admonition failure >}}
Execution Timeout Expired. The timeout period elapsed prior to completion of the operation or the server is not responding.
{{< /admonition >}}

{{< admonition failure >}}
The remote server returned an error: (500) Internal Server Error.
{{< /admonition >}}

Resolution, you can simply stop and start the services manually:

* World Wide Web Publishing Service
* Microsoft Dynamics 365 Unified Operations: Batch Management Service

Or you can restart VM using LCS or Azure portal.

If the issue **Can not stop the DynamicsAXBatch service on computer** still persist

You can kill the batch service using cmd:

1. Open CMD
2. `sc queryex DynamicsAxBatch`
3. `taskkill /f /pid 8064`

![Image](queryex.png "queryex")

There is another error about Database

{{< admonition failure >}}
Cannot open database "AxDB" requested by the login. The login failed.
Login failed for user 'axdbadmin'.
{{< /admonition >}}

Please this post for getting [VHD Database password](https://nuxulu.com/2020-04-06-getting-onebox-vhd-dynamics-365-finance-and-operations-virtual-machine/#2-rename-vm).

2. After 10.0.24

The Admin user provisioning tool is typically used to change the tenant of the environment. You can update the sign in information in the database for the Admin user or any other user. You only need the `SID` and `network alias` (email address) from a user that can access the environment or another environment on the same tenant.

To get the SID:

```ps
wmic useraccount get name,sid
```

![Image](get-sid-cmd.png "get-sid-cmd")

Once you have admin SID, you can update the tenant in SQL by using.

```sql
select ID, SID, NETWORKALIAS from USERINFO where ID = 'Admin'

update USERINFO set SID = 'new_SID', NETWORKALIAS = 'new_NetworkAlias' where ID = 'Admin'
```

