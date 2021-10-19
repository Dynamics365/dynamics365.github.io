# What is the difference difference between menu item Display, Output and Action


The answer is that there is no difference, the difference is a conceptual one rather than a functional or a technical one. So I think you will make a better choice for your scenario base on functional side.

![what-is-the-difference-difference-between-menu-item-display-output-and-action-in-dynamics-ax](what-is-the-difference-difference-between-menu-item-display-output-and-action-in-dynamics-ax.png "hello") 

### Display Menu item

This folder is used to contain menu items that reference runnable application objects that primarily present forms, ddialog and so on, to the user. May be this forms, dialog called from another forms.

### Output Menu item

An output menu item application objects whose primarily function is to print a result or report.

### Action Menu item

You should create a menu item under this folder if your runnable application objects whose primarily function is to do some kind of a job, such as creating or updating transactions in the database.
