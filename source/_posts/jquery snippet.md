---
title: jquery snippet
date: 2017-08-05 22:00:30
tags: snippet
categories: jquery
---

### 语法

- 设置标签属性
  
  `$("div").attr("属性名","属性值");`

- 设置标签样式
  
  `$("div").css("color","yellow"); `或`$("div").css("padding-left"))`

- 移除属性
  
  ` $("div").removeAttr("id");`

- 遍历数组
  
  - 原生js有两种方法都可以使用[for(var i;i<arr.length;i++){},for(var i in arr){}] 
  
  - jquery有两个函数共计四种方法都可以使用
    
    ```js
    $.each(arr,function(i,item){}),
    $(arr).each(function(i,item){}), 
    $.map(arr,function(i,item){}), 
    $(arr).map(function(i,item){})
    ```

- 遍历对象 
  
  - 原生js有一种方法可以使用[for(var i in obj){}] 
  
  - jquery有两个函数共计两种方法可以使用
    
    ```js
    $.each(obj,function(i,item){})
    $.map(obj,function(i,item){})
    ```

- Jquery中的ajax在默认不写async情况下，请求为异步请求；即：async:true

- `$(function(){ })`是定义一个匿名函数，它是`$(document).ready(function(){})`的简写