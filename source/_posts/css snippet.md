---
title: css snippet
date: 2016-01-16 20:30
tags: snippet
categories: css
---

### display
每个元素都有一个默认的 display 类型
不过你可以随时随地的重写它！如常见的例子是：把 li 元素修改成 inline，制作成水平菜单。

p标签是块级元素，span元素是行内元素。内元素可以写在块级元素里面  比如`<p><span>内容</span></p>`

- block

值为block的为块级元素， 如：p、form、header、footer、section

- inline

值为inline的为行内元素

### position

- static

static是默认值，一个static元素表示它不会被"positioned",一个position属性被设置为其它值得元素表示它会被"positioned"

- relative

relative,在一个相对定位（position属性的值为relative）的元素上设置 top 、 right 、 bottom 和 left 属性会使其偏离其正常位置。
其他的元素的位置则不会受该元素的影响发生位置改变来弥补它偏离后剩下的空隙。

- fixed

fixed,一个固定定位（position属性的值为fixed）元素会相对于视窗来定位，这意味着即便页面滚动，它还是会停留在相同的位置。
和 relative 一样， top 、 right 、 bottom 和 left 属性都可用。

- absolute

absolute, 它与 fixed 的表现类似，但是它不是相对于视窗而是相对于最近的“positioned”祖先元素。
如果绝对定位（position属性的值为absolute）的元素没有“positioned”祖先元素，那么它是相对于文档的 body 元素，并且它会随着页面滚动而移动。
记住一个“positioned”元素是指 position 值不是 static 的元素。

### mbp

![](https://mdn.mozillademos.org/files/13647/box-model-standard-small.png)

- margin

  > 是设置两个标签的间隔，也就是距离

- padding

  > 比如一个p标签，它是100px\*100px,我们使用的时候p标签的文字是贴着p标签的最左侧的，想要它的文字距离边界远一点，好看一点，就是需要做一个样式的调整，我们可以用到padding这个属性，假如说设置是10px，就是说这个p标签的大小依然是100px*100px，但是它的内容是变成了90px\*90px；

- border

  > boarder是设置边框的意思，他和padding的区别在于，padding是在标签边缘往里缩减，而border是在标签的边缘往外扩展，也就是说是一个100px\*100px的标签，我设置他的border为20px，我们就可以看到整个标签的大小是变成了120px\*120px，也就是说多出来了120px，其标签的内容也还是100px\*100px的
