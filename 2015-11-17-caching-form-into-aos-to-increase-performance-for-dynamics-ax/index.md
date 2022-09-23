# Caching form into AOS to increase performance for Dynamics AX


**Scenario:**

Normally when we open form for the first time in Dynamics AX, it will take sometimes to compilte and cache into AOS. That's why at second time always faster.
If we often restart AOS, how can we keep performance ?


**Solution:**

After restart AOS we can let some scripts run to open up some often used forms then cache to AOS, then close those forms.

Here is the code

```csharp
static void WarmupRF(Args _args)

{
    UtilElements e;
    TreeNode treeNode;
    FormRun formRun;

    Args args = new Args();

    while select e
        where e.utilLevel  == UtilEntryLevel::var    //<-- specify layer here
        &&    e.recordType == UtilElementType::Form  //<-- and only forms
        &&    e.name like "nameofformPrefix"

        {
            try
            {
                treeNode = xUtilElements::getNodeInTree(xUtilElements::parentElement(e));
                args.name(treeNode.AOTname());
                formRun = ClassFactory.formRunClass(args);
                formRun.init();
                //formRun.run();   //<-- No need to run the form, but sometimes it can load the data
                formRun.close();
            }
            catch
            {
                Infolog.clear();
                continue;
            }
        }
}
```
