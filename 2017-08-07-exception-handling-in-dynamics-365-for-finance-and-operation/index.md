# Exception Handling in Dynamics 365 For Finance and Operation


When we write a code, or make a customization to Dynamics 365 For Operation which is using X++ should make use of Exception Handling to provide some context for the message or a different more useful message. In this article, I will be exploring how I can come up with a uniform way to catch multiple types of exception that can be raised in X++.

### 1. Exception type
There are many several types of exception and the type differs depending on what caused the error. Much of exception types are determined by the kernel and are not normally thrown by application code. All **exception types**, however, can be caught, and it is the developers’ responsibility to decide which exceptions need to be handled.

The exception type is identified using the system-based enumeration called an exception. Because it is a system Enum, it cannot be modified, so users cannot add new exception types.

The following table shows the exception literals that are the values of the Exception enumeration.

<figure class='center '>
  <a href="/imagesposts/2017-08-07_10h00_37.png"><img src="/imagesposts/2017-08-07_10h00_37.png" alt=""></a>
</figure>

<!--more-->

### 2. Key commands
The **Try** command signifies the start of a block of code that you want to control with the X++ exception handling system. Any exceptions that are thrown in that block of code can be caught and handled accordingly. The block of code inside the Try statement must be contained between brackets ( { } ).

 **Catch** statements come after the block of code and define what code is executed when each exception is thrown.
 
You do not have to define a catch statement for every possible exception; however, each try statement must have at least one catch statement.

 A **Retry** command tells the system to go back to the Try statement and attempt to execute the code again. Any data that was loaded before the Try command will remain as it was, but any data retrieved or modified after the Try statement will be refreshed.
 
When a deadlock exception is thrown, all locks on tables that this process holds are released, which may allow the other process or processes that are also deadlocked to continue.

By calling a retry, the process can attempt to update the record again and may now be able to complete. It is a best practice that a retry uses a counter so that the number of retries can be controlled, and a user does not become stuck in a loop.

The **final** keyword is now available to follow the try and catch keywords. The semantics are identical to the semantics in C#. The statements provided in the final clause is executed irrespective of whether the try block threw any exceptions.


### 3. Code Statement
We will use these lines of code example here for testing Exception handling

```C#
class CustCreateCustomer
{
    public static void main(Args _args)
    {
        CustCreateCustomer custCreateCustomer = new CustCreateCustomer();
        custCreateCustomer.run();
    }

    public void run()
    {
        Dialog          dlg = new Dialog("Create new customer");
        DialogField     dlgCust;
        DialogField     dlgGrp;
        CustTable custTable;

        dlgCust = dlg.addField(extendedTypeStr(CustVendAc), "Customer account");
        dlgGrp  = dlg.addField(extendedTypeStr(CustGroupId));
        
        if (dlg.run())
        {
            try
            {
                custTable.AccountNum    = dlgCust.value();
                custTable.CustGroup     = dlgGrp.value();
                if (!custTable.validateWrite())
                {
                    throw error("Please enter all required fields.");
                }
                else
                {
                    custTable.insert();
                }
            }
            catch (Exception::Error)
            {
                error("An error occurred. Please try again");
            }

        }
    }
}
```

This code will try to create a customer after revived inputted value from users, this code also handling errors when user do not input enough information. A `Throw` statement is used to throw an error that can be caught by a Catch statement. When the system throws an exception `ttsabort` is called automatically, and so does not have to be called in a Catch statement.

### 4. Optimistic Concurrency Exceptions
The **optimistic concurrency check** (OCC) is a performance enhancing function within Microsoft Dynamics 365 For Operation. It presumes that any record retrieved from the database is not updated until it is proven to be updated by the database. This means that fewer locks must be placed on records in the database. This allows for faster access for other users.

This also means that one user can update a record after another user has retrieved it from the database. This can cause data inconsistency. If the second user then also tries to update the record, an `UpdateConflict` exception is thrown. The system does this by comparing the `recVersion` field on the record buffer at runtime and the actual record in the database. The `recVersion` field value is changed every time that an update is successfully made to a record.

There are two main table update exceptions, `UpdateConflict` and `DeadLock`. An update conﬂict occurs due to the optimistic concurrency failing, whereas a deadlock is the classic database scenario where both transactions have each locked a table that the other needs.

**Update conﬂicts** are normally handled within the insert, delete, and update methods of a table. The **BOM table** is a good example of this. You may also hard to find many examples where this has been used. We use this pattern only if we deem it to be required. The code within the table's update method also updates other records, so it has been written to handle update conﬂicts.

The following code is an example of how to handle the UpdateConflict exception that might be thrown.

```C#
public void update()
{
    #OCCRetryCount
    try
    {
        ttsbegin;
        // code that updates records in other tables
        super(); // do the update
        // other code that updates records in other tables
        ttscommit;
    }
    //Deadlock
    catch (Exception::Deadlock)
    {
        retry;
    }
    //UpdateConflict
    catch (Exception::UpdateConflict)
    {
        if (appl.ttsLevel() == 0)
        {
            if (xSession::currentRetryCount() >= #RetryNum)
            {
                throw Exception::UpdateConflictNotRecovered;
            }
            else
            {
                retry;
            }
        }
        else
        {
            throw Exception::UpdateConflict;
        }
    }
}
```

If a conflict due to OCC occurs, the system throws the `UpdateConflict` exception and it is caught by the catch statement.

The other new element here is `ttsLevel`. Since transactions can be nested, we do want the exception to falling through to the parent transaction if one exists. If `ttsabort` is issued (directly or due to a throwing error) at any level, the whole transaction will be rolled back; we can't roll back just the level where the error is thrown.
 
The code checks the current TTS level. If it is not Zero, in other words, it is still in a TTS transaction, it throws another `UpdateConflict` exception to the next Catch list of the next outer Try statement in scope. This continues until it is no longer inside a TTS transaction. We must make sure that when the code is retired, all data is refreshed.

It is important that we don't retry indefinitely, as this may cause the client to hang. To control this, we use `xSession::currentRetryCount()` to get the number of retries and check this against the `#RetryNum` macro. The macro defines the standard number of retries deemed appropriate by Microsoft, which is five.
then the `UpdateConflictNotRecovered` exception is thrown. This means the whole transaction is aborted and stops retrying. 


### 5. Conclusion
We do not, in any case, want an error to be thrown that stops the form from opening. Also, if there is an error, we need to decide whether the user actually needs to know that an error occurred. It may be enough for our purposes that the fields don't appear, and we can use the debugger to trace through the code to determine why.

Thank you for reading!
