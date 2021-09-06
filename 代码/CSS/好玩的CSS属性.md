##  1、all

你用过CSS 框架吗？如果是的话，我很确定你可能曾经想根据自己的喜好覆盖某些元素。

而最常见的方法是使用 CSS 中的 !important 属性来强调当前属性，而忽略所有其他设置和规则。

```css
.header{
	color: blue !important;
	font-size: 14px !important;
}
```

但是，一遍又一遍地编写相同的关键字会使 CSS 文件看起来很混乱。

而更简单的方法是使用 all 属性。

它共有 3 个属性值——initial、inherit 和 unset。

```css
.header{
	all:initial;
	color: blue;
	font-size: 14px;
}
```

all:initial 将元素的所有属性设置为其退回或初始值。

Chrome 和 Firefox 分别从版本 37 和版本 27 开始支持此属性。Edge 浏览器也支持此属性，但 Internet Explorer 不支持。
## 2、writing-mode

我最近写了一篇关于寻找设计灵感的神奇地方的文章，我偶然发现了许多文本垂直和横向排列的网站。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/eXCSRjyNYcY2kgFeTaFDbS14rsjGHLqx2YKLkAcMDyUgMRByibULjD7T3kwdVEKKokmP8DqJ1ZUt73oHyLmywfQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在上图的右侧（靠近滚动条），你可以看到横向放置的文本，这是显示附加信息的一种巧妙方式。

writing-mode属性可以帮助你实现这一点。

此属性支持以下值：

sideways-rl：文本和其他内容从上到下垂直排列，并向右侧放。

sideways-lr：与 sideways-rl 一样，文本和其他内容从上到下垂直排列，但向左侧向放置。

Vertical-rl：文本和其他内容从上到下垂直排列，从右到左水平排列。如果有两行或更多行，则将这些行放置在前一行的左侧。

Vertical-lr：与 vertical-rl 不同，文本从左到右横向排列，如果有两行或更多行，则将这些行放在上一行的右侧。

还有horizontal-tb 表示文本显示的标准方式。

![图片](https://mmbiz.qpic.cn/mmbiz_png/eXCSRjyNYcY2kgFeTaFDbS14rsjGHLqxnUyGKKF9futhK9zZlFDxhZrHicz0RYREVhuAwxagNf3vHZTdsB6eGfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

你可以在这个[地址](https://developer.mozilla.org/en-US/docs/Web/CSS/writing-mode)，找到实现和代码片段。