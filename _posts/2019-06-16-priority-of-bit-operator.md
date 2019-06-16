---
layout: post
title: 位操作优先级
categories: [C++]
description: some word here
keywords: keyword1, keyword2
---

在c++中，位操作符（&，|和^）的优先级比相等运算符(==和!=)要低！！！
因此，在进行位操作的判断时候，一定要在判断相等前面加上括号，否则会先进行相等运算符，然后再进行位操作运算。