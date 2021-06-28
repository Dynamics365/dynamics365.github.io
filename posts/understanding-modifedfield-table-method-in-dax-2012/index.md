# Understanding modifedField() Table method in DAX 2012


Dear all,

We use `modifiedField()` method to perform any actions  after the field is modifed  

*Ex:*

Create new table with 2 fields **ItemId** and **ItemNameDisplay**  from Extended Data Types node in AOT then Override `modifiedField()` in Table's method node, something likes:

``` c
public void modifiedField(FieldId _fieldId)
{
    InventTable inventTable;
    super(_fieldId);
    switch (_fieldId)
    {
        case fieldNum(IBD_Invent,itemid):
            this.ItemNameDisplay = inventTable::find(this.itemid).NameAlias;
            break;
    }
}
```
<!--more-->
the `modifiedField()` method is located on tables and it is called by a form (or a dataset) when any field is changed. It has field ID as a parameter and therefore it is very easy to write code reacting to a change of a particular field. It is so easy that the `modifiedField()` method is often used even in such cases, when it really shouldn’t be used.  

If you want to get previous field value in the `modifiedField()` for comparative purpose, you can use `this.orig()` method:

```c
public void modifiedField(fieldId _fieldId)
{
    super(_fieldId);
    info(strfmt("Field number %1 changed from %2 to %3",_fieldId,this.orig().(_fieldId),this.(_fieldId)));
}
```

