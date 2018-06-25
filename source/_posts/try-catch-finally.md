---
title: try catch finally的执行顺序
date: 2018-06-25 18:44:46
tags: java知识点
categories: 点滴
---

1. **不管有木有出现异常，finally块中代码都会执行** 
2. **当try和catch中有return时，finally仍然会执行** 
3. **finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的；** 
4. **finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。** 
5. 任何执行try 或者catch中的return语句之前，都会先执行finally语句，如果finally中有return语句，那么程序就return了，所以finally中的return是一定会被return的。