---
title: DefaultValue ComboBox in Dynamics AX with X++
layout: post
date: 2015-11-13 06:57:46.000000000 +07:00
author: Max Nguyen
categories: [ax2012]
tags: [operator, programming]
---

Beside modify metadata we can use code like below.

You can use this code in the form's init method after `super()`:

`ComboBoxName.selection(ComboBoxName::DefaultValue);`


If this is a table field we should you best practice overriding the `initValue` method in the table:

`this.ComboBoxName = ComboBoxName::DefaultValue;`


Override `initValue` in the form's datasource only if it should be a specific behaviour in this form only.