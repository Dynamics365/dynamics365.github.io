---
layout: post
title: Number sequence farmework overview in Dynamics AX 2012
description: How to create number sequence in Dynamics AX 2012 with difference scenarios
author: Max Nguyen
modified: 2017-01-19
categories: [ax2012, trick, tools]
tags: [xpp, numbersequence, programming]
---

![Step by step how to create Number sequence.](https://dynamics365.github.io/assets/number-sequence-overview.png)

For this demo, I will create New module, about creating Number Sequence without module is also same steps just leave some steps base on Design picture above

1.	Create ETD `ContosoId` extends `num` datatype.

2.	Create Contoso Table with ContosoId field.

3.	Create a new enum value Contoso in base ENUM NumberSeqModule.

This value will be used to link number sequence to the module and to restrict displayed number sequence by module in Form.
