---
title: html snippet
date: 2017-02-10 12:01:30
tags: snippet
categories: html
---

### label

- chrome页面跳转，需要使用 i 标签，不能使用 button 标签，因为在button的点击事件中，window.location.href不起作用。（但firefox起作用）

### css

#### mbp

- margin

  > 是设置两个标签的间隔，也就是距离

- padding

  > 比如一个p标签，它是100px\*100px,我们使用的时候p标签的文字是贴着p标签的最左侧的，想要它的文字距离边界远一点，好看一点，就是需要做一个样式的调整，我们可以用到padding这个属性，假如说设置是10px，就是说这个p标签的大小依然是100px*100px，但是它的内容是变成了90px\*90px；

- border

  > boarder是设置边框的意思，他和padding的区别在于，padding是在标签边缘往里缩减，而border是在标签的边缘往外扩展，也就是说是一个100px\*100px的标签，我设置他的border为20px，我们就可以看到整个标签的大小是变成了120px\*120px，也就是说多出来了120px，其标签的内容也还是100px\*100px的

### 块级、行级

- p标签是块级元素，span元素是行内元素。内元素可以写在块级元素里面  比如`<p><span>内容</span></p>`