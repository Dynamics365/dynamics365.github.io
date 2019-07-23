---

title: AxBuild.exe for Parallel Compile on AOS
description: AxBuild.exe for Parallel Compile on AOS
author: Max Nguyen
modified: 2015-07-01
categories: [ax2012, trick]
tags: [axbuild, trick, aos]
key: d365
---

If you have only 10 mins to build ax, try this

In AOS server, go to `C:\Program Files\Microsoft Dynamics AX\60\Server\DAX\bin` and open `cmd` from here then run this command

{% highlight yaml %}
axbuild.exe xppcompileall /s=01 /altbin="C:\Program Files (x86)\Microsoft Dynamics AX\60\Client\Bin"
{% endhighlight %}

![]({{site.url}}/assets/imagesposts/axbuild-exe-for-parallel-compile-on-aos_2.png)

Result

![]({{site.url}}/assets/imagesposts/axbuild-exe-for-parallel-compile-on-aos_3.png)

Once compile complete, you can import compile log file at `C:\Program Files\Microsoft Dynamics AX\60\Server\DAX\log` into compiler output of AX client

![]({{site.url}}/assets/imagesposts/axbuild-exe-for-parallel-compile-on-aos_4.png)

*Reference from* [MSDN](https://msdn.microsoft.com/en-us/library/dn528954.aspx) .