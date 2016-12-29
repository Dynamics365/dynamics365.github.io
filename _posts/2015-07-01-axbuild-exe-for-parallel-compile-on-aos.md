---
layout: post
title: AxBuild.exe for Parallel Compile on AOS
description: AxBuild.exe for Parallel Compile on AOS
author: Max Nguyen
modified: 2015-07-01
categories: [ax2012, trick]
tags: [axbuild, trick, aos]
---

If you have only 10 mins to build ax, try this

go to `C:\Program Files\Microsoft Dynamics AX\60\Server\DAX\bin` and open `cmd` from here then run this command

![](https://dynamics365.github.io/assets/axbuild-exe-for-parallel-compile-on-aos_2.png)

{% raw %}
axbuild.exe xppcompileall /s=01 /altbin="C:\Program Files (x86)\Microsoft Dynamics AX\60\Client\Bin"
{% endraw %}

Run at AOS server

Result

![](https://dynamics365.github.io/assets/axbuild-exe-for-parallel-compile-on-aos_3.png)

Reference from [MSDN](https://msdn.microsoft.com/en-us/library/dn528954.aspx) .