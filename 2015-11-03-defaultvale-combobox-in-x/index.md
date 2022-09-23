# DefaultValue ComboBox in Dynamics AX with X++


Beside modify metadata on form properties, as best practice we can use code like below to assign default value for combobox.

* You can use this code in the form's init method after `super()`:

```C#
ComboBoxName.selection(ComboBoxName::DefaultValue);
```

* If this is a table field we should you best practice overriding the `initValue` method in the table:

```C#
this.ComboBoxName = ComboBoxName::DefaultValue;
```

* Override `initValue` in the form's datasource only if it should be a specific behaviour in this form only.
