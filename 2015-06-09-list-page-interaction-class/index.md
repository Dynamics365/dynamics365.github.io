# List Page Interaction Class


Form interaction classes that allow user interface control logic to be shared across forms. For instance, controlling which buttons are available to a list page and the associated detail form.

The interaction classes are extending a base ListPageInteraction class. This has some methods supported by the kernel to interact e.g. with initializations of the list page form. Other classes can be build stand alone to execute e.g. a batch process or represent a web service or posting classes.

Form interaction classes are not mandatory for list pages but should be used on data entry forms that require logic. This ensures consistency and allows easier maintenance of logic.

 This class inherits `from SysListPageInteractionBase`, here is some methods we need to know

* **initializing**: Called when the form is initializing – Similar to the form init method

* **intializeQuery**: Also called when the form is initializing – Similar to the datasource init method.

* **selectionChanged**: Called when the active record changes – Similar to the datasource active method.

* **setButtonEnabled**: Should be overridden to dynamically enable/disable buttons based on the current selection. This is called from the selectionChanged method.
<!--more-->
```C#
public void selectionChanged()
{
    Requisition requisition = this.listPage().activeRecord(queryDataSourceStr(RequisitionQuery,Requisition));
    super();
   
    if(requisition.WorkflowApprovalStatus == WorkflowApprovalStatus::Approved)
        this.listPage().actionPaneControlEnabled(formControlStr(RequisitionListPage,Edit),true);
    else
        this.listPage().actionPaneControlEnabled(formControlStr(RequisitionListPage,Edit),false);
}
```

* **setButtonVisibility**: Should be overridden to show/hide buttons when the form first opens. This is used more to do a one-off layout adjustment based on system configuration/parameters, as well as the menu-item used to open the form. 

eg If you have a menu-item that opens a form based on status, you may want to hide the relevant `status` field to reduce clutter.

Thank you for reading!
