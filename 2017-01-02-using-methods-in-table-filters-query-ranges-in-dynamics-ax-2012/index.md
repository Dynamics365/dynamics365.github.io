# Using Methods in Table Filters & Query Ranges in Dynamics AX 2012


In Dynamics AX, there is a class called `SysQueryRangeUtil` that can be utilized in both query ranges and table filters. Using methods from this class allow you to be very precise about what dates you want to use in reports or for filtering your data.

Let’s say you have a report that you always want to run to see orders with shipping dates of the next day. It is possible to do so by using one of the methods from the `SysQueryRangeUtil`. The use of the letter **'t'** will work for today’s date, but when you try to add days to it, it doesn’t work in reports. Instead, I will use the `currentdate()` method and add `1` to it.

> All methods & expressions must be surrounded by parentheses as shown below.

![Figure 1 – Filtering the requested ship dates in an AX query for tomorrow](/imagesposts/Using-Methods-in-Table-Filters-Query-Ranges-in-Dynamics-AX-2012.jpg)

*Filtering the requested ship dates in an AX query for tomorrow `(current day() + 1)`*

On any form with a grid, you filter your data by pressing **_Ctrl+G_**. 

If I were to want to see open customer invoices from the last 90 days, I would filter my **open customer invoices form** and use the method `(dayRange(-90,0))`. The first number represents how many months backward from this month, and the second represents how many months forward. The same sorts of things can be done for the `monthRange()`, `yearRange()`, and `dateRange()` methods. The best part about this is that you can of course save these filters to create views that you might use on a daily basis.

![Figure 2 – Searching the grid for the past 90 days](/imagesposts/Using-Methods-in-Table-Filters-Query-Ranges-in-Dynamics-AX-2012-2.jpg)

If you are creating your query ranges in code, these methods can also be utilized whenever you are setting them. Definitely be sure to check out the `SysQueryRangeUtil` class as there are many more methods to use.

Here is some methods you can use:

```html
currentCustomerAccount()

currentVendorAccount()

currentUserId()

currentDate()

dateRange()

day()

dayRange()

greaterThanDate()

greaterThanUtcDate()

greaterThanUtcNow()

lessthanDate()

lessthanUtcDate()

lessthanUtcNow()

monthRange()

yearRange()
```

> for example:

```powershell
(dayRange(-30,0)) – Results in a date range for the last 30 days: "26-01-2017".."25-02-2017"

(day(-1)) – Results in yesterday's date: 24-02-2017

(day(0)) – Results in today's date: 25-02-2017

(day(1)) – Results in tomorrow's date: 26-02-2017

(greaterThanDate(2)) – Results in every date after today plus 2: > 27-02-2017

(lessThanDate(-1)) – Results in every date of today minus 1: < 24-02-2017

(monthRange(0,2)) – Results in first day till the last day of the month's choosen (0 = current month): "01-02-2017".."30-04-2017"

(yearRange(-1,-1)) – Results in first day till the last day of the chosen year: "01-01-2017".."31-12-2017"
```

Thank you for reading!

