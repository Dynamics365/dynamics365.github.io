# How to skip standard code in Dynamics 365 finance and operations


Disclaimers: Try at your own risk, this is consider as a bug and it will be fixed soon.
{:.error}

According to this MS article about Chain Of Command <https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/extensibility/method-wrapping-coc#wrapper-methods-must-always-call-next>

> "Wrapper methods must always call next Wrapper methods in an extension class must always call next, so that the next method in the chain and, finally, the original implementation are always called. This restriction helps guarantee that every method in the chain contributes to the result. In the current implementation of this restriction, the call to next must be in the first-level statements in the method body."

So as described in the article, the Chain Of Command in Dynamics 365 finance and operations is used to add the logic before or after a method of a class is invoked and the original method will always be executed due to the compulsory of the next statement.
When doing some implementations, we found out that there is a tricky way to completely **ignore** the execution of the original method of the class that is extended.

Please see below for more details:
For example, I created a project and add a new runnable class named Alex:

```cs
class Alex
{
    public static void main(Args _args)
    {
        throw error ("Hello World");
    }
}
```

When this class runs, a _Hello World_ info message will be displayed in the FinOps UI. Then I used chain of command to create Alex_Extension class that extends this class and "override" the main method of the Alex's class by putting the next call into another method and never call it like this:

```cs
[ExtensionOf(classStr(Alex))]
final class Alex_Extension
{
    public static void main(Args _args)
    {
        //Implement something here
        info ("Ok");
        void ignore()
        {
            next main(_args);
        }
    }
}
```

When I run the _AlexExtension_, the original main method of the Alex class will be ignored and only the additional block of code that I wrote in the _AlexExtension_ class will be executed, so only the **OK** info will appear.

Now we will try with a sample scenario from D365 Finance and Operation. In **General Ledger** Module, we have **General journals** form

![Image](/imagesposts/How-to-ignore-standard-code-in-Dynamics-365-finance-and-operations.png)

When you click the button for **Journal Print**, the menu item will be triggered and point to the controller name: *LedgerJournalController* with *LedgerJournalController* class and the report will be generated.

I will use COC to “override” the main method of *LedgerJournalController* class by applying the method above:

```cs
[ExtensionOf(classStr(LedgerJournalController))]
final class Alex_LedgerJournalController_Extension
{
    public static void main(Args _args)
    {
        info("ok");
        void ignore_first()
        {
            next main(_args);
        }
    }
}  
```

Using this I will ignore the the main method of *LedgerJournalController* class and instead of generate the report, the result will be only the **OK** info line in the extension class:

![Image](/imagesposts/How-to-ignore-standard-code-in-Dynamics-365-finance-and-operations-1.png)

This is a little bit tricky because we still call next in our extension class as per the requirement of COC, but as we never call the method that "wrap" the next call so it will not be executed. You can try this to simply ignore or rewrite a method of a class in FinOps.

Thank you for reading.


