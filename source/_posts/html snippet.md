---
title: html snippet
date: 2017-02-10 12:01:30
tags: snippet
categories: html
---

### label

- chrome页面跳转，需要使用 i 标签，不能使用 button 标签，因为在button的点击事件中，window.location.href不起作用。（但firefox起作用）

- textarea标签里面的属性要紧靠书写，且不要换行，不然，会显示textarea默认赋值为多个空格

  ```html
  <textarea type="text"class="form-control"id="obj_address"placeholder="地址（必填）"name="address"isvalid="yes"checkexpession="NotNull"errormsg="地址"></textarea>
  ```

