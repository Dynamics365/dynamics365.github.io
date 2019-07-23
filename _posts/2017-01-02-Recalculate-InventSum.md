---

title: Recalculate InventSum in Dynamics AX
description: Recalculate InventSum in Dynamics AX
author: Max Nguyen
modified: 2017-01-02
categories: [ax2012, trick, tools]
tags: [inventsum, expression, trick, xpp, programming]
key: d365
---

`InventSum` is needed to recalculate sometimes. We should use `InventSumRecalcItem` class in Dynamics AX.

{% highlight csharp %}

InventSumRecalcItem InventSumRecalcItem;
;
InventSumRecalcItem = new InventSumRecalcItem("ITEM001", true, checkfix::fix);
InventSumRecalcItem.updatenow();
{% endhighlight %}

First parameter : `ItemId`

Second parameter : `Show errors`

Third parameter : `Fix or only check`

What if you want to calculate for all items:

{% highlight csharp %}
InventTable InventTable;
InventSumRecalcItem InventSumRecalcItem;

WHILE SELECT InventTable
	WHERE (InventTable.ItemType == ItemType::Item) || (InventTable.ItemType == ItemType::BOM)
	{
		InventSumRecalcItem = new InventSumRecalcItem(InventTable.ItemId, true, checkfix::fix);
		InventSumRecalcItem.updatenow();
	}
{% endhighlight %}

Thank you for reading!
