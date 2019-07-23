---

title: X++ Performance tips
description: X++ Performance tips
author: Max Nguyen
modified: 2015-02-12
categories: [ax2012, trick]
tags: [trick, xpp, programming]
key: d365
---

### Tip 1: Measure execution time of your code

Measuring is knowing. Before you start changing code, make sure you have a set of data you can keep reusing for your tests. Measure the performance of your code on that data after each change in code so you know the impact of your changes.

One way to do this is by using the `Winapi::getTickCount()` or `WinApiServer::getTickCount()` if your code runs on server method.

{% highlight csharp %}
static void KlForTickCountSample(Args _args)
{
    int ticks;
    ;
    // get the tickcount before the process starts
    ticks = winapi::getTickCount();
    
    // start the process
    sleep(2000); // simulate 2 seconds of processing
    
    // compare tickcount
    ticks = winapi::getTickCount() – ticks;
    
    // display result
    info(strfmt('Number of ticks: %1', ticks));
}
{% endhighlight %}
<!--more-->
### Tip 2: limit the number of loops

A LOT of time goes into loops. If you have a performance problem, start looking for loops. Code can run really fast, but it can get slow when it is executed too many time, eg, in a loop.

### Tip 3: avoid `if` in `while select`

When there is a `if` in a `while select`, see if you can rewrite it a a where statement in your select. Don’t be affraid use a join either. Consider the following example:

{% highlight csharp %}
static void KlForIfInLoop(Args _args)
{
    VendTable vendTable;
    ;
    // usually slower
    while select vendTable
    {
        if(vendTable.VendGroup == 'VG1')
        {
            info(vendTable.AccountNum);
        }
    }

    // usually faster
    while select vendTable
    where vendTable.VendGroup == 'VG1'
    {
        info(vendTable.AccountNum);
    }
}
{% endhighlight %}

### Tip 4: avoid double use of table methods

Using table methods a lot can get really slow if you do it wrong. Consider the following example:

{% highlight csharp %}
static void klForTableMethodsSlow(Args _args)
{
    SalesLine salesLine;
    InventDim inventDim;
    ;
    
    // select a salesline
    select firstonly salesLine;
    
    inventDim.InventColorId = salesLine.inventDim().InventColorId;
    inventDim.InventSizeId  = salesLine.inventDim().InventSizeId;
    inventDim.inventBatchId = salesLine.inventDim().inventBatchId;
}
{% endhighlight %}


This example code looks nice, but there’s a problem. `The salesLine.inventDim()` method contains the following:

{% highlight csharp %}
InventDim inventDim(boolean  _forUpdate = false)
{
    return InventDim::find(this.InventDimId, _forUpdate);
}
{% endhighlight %}

This means that the `invendDim` record is read three times from the database. It is better to declare the `inventDim` record locally and only retrieve it once:

{% highlight csharp %}
static void klForTableMethodsFast(Args _args)
{
    SalesLine salesLine;
    InventDim inventDim;
    InventDim inventDimLoc;
    ;
    
    // select a salesline
    select firstonly salesLine;
    
    inventDimLoc = salesLine.inventDim();
    
    inventDim.InventColorId = inventDimLoc.InventColorId;
    inventDim.InventSizeId  = inventDimLoc.InventSizeId;
    inventDim.inventBatchId = inventDimLoc.inventBatchId;
}
{% endhighlight %}

### Tip 5: Don’t put too much code on tables

Code on tables is usually fast, but things can get slow if you use it to much. Say you have a table with an `InventDimId` field. If you have 5 methods that need the `InventDim` record, because you don’t have a classDeclaration method on your table, you need to call this function 5 times, once in every method:

{% highlight csharp %}
InventDim::find(this.inventDim)
{% endhighlight %}

When you put these methods on a class, you could optimise it by fetching the record only once and storing it in the `classDeclaration`, or better, passing it as a parameter to your methods. An other example is fetching parameters from parameter tables, eg `InventParameters::find()`. On a table, you have to fetch it each time you call a method. In a class, you would probably optimize your code to only fetch the parameter record once.

### Tip 6: Use the fastest code

For some tasks, there is **special code** that is faster than the code you would normally write. For example:

{% highlight csharp %}
// slower
while select forupdate custTable
where custTable.custGroup == 'TST'
{
    custTable.delete();
}
// faster
delete_from custTable
where custTable.custGroup == 'TST';
{% endhighlight %}

The same applies to update_recordset for updating records. Also, when adding values to the end of a container 
{% highlight csharp %}
cont += "a value";
{% endhighlight %}

 is faster than 
 
 {% highlight csharp %}
 cont = conins(cont, conlen(cont), "a value");
 {% endhighlight %}
 
 
### Tip 7: Every optimization counts
 
Remember that every optimization you do to you code counts, even if it’s a little one. Small performance tweaks can have a huge effect once you process large quantities of data. So don’t be lazy, and optimize.


Share from http://www.artofcreation.be/