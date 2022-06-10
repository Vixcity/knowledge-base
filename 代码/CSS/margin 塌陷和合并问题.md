## margin 塌陷和合并问题
### 前言

小问题总是被人视而不见，等遇到的时候又突然想不起来

所以我们需要经常总结，这可以帮助我们更好的记忆东西

今天我们来重新回顾一下两个不起眼的小问题：`margin`塌陷和`margin`合并
### margin塌陷
#### 什么是margin塌陷

我们先来看个例子，然后引出什么是`margin`塌陷

需求：在父子元素中，通过`marigin`让父元素相对左边及顶部各距离`100px`,也让子元素相对于父元素左边和顶部各`50px`

代码如下：
```css

div.father{ 

 width: 200px; 

 height: 200px; 

 background-color: rgb(219, 68, 101); 

 margin-left: 100px; 

 margin-top: 100px; 

} 

div.father div.son{ 

 width: 100px; 

 height: 100px; 

 background-color: rgb(56, 248, 207); 

 margin-left: 50px; 

 margin-top: 50px; 

}

```
代码看上去没有什么问题，看下效果

![](https://s2.loli.net/2022/06/10/vstTKwqAOZd1L5f.webp)

惊奇的发现，结果出乎我们意料！

水平方向的距离确实没问题

但是垂直方向上，明明子元素设置了`margin-top`距离顶部`50px`

按道理它会距离父元素顶部`50px`才对。

这种现象被称之为`margin`塌陷

但很多人可能都不太理解，为什么叫它塌陷？

其实你可以这样理解，如上述例子，原本子元素要距离父元素顶部`50px`，现在没有了

也就是父元素的顶棚对子元素来说没有作用，相当于塌陷下来了

所以叫margin塌陷

一句话总结：
> 父子嵌套的元素垂直方向的`margin`取最大值。

但是有的时候我们不希望有这样的问题，那我们如何解决`margin`塌陷的问题呢？

答案是，我们可以通过触发`BFC`来解决！那是什么是`BFC`呢？
#### BFC
##### 什么是BFC和作用

`Block Formatting Context`，中文叫块级格式上下文

`CSS`将`HTML`的每一个元素都当成一个盒子，而且它进一步的认为每一个盒子里面都有一套正常的语法规则或者叫渲染规则

它能根据这个规则将写的HTML元素绘制出来，但是我们可以通过一些特定的手段触发`BFC`

让其所符合的语法规则和原来的语法规则有点不一样

也就是触发`BFC`之后，特定的盒子会遵循另一套语法规则

`BFC`听起来挺神奇，能改变一个盒子的语法规则，那到底改变了多少呢？

其实也没改变多少

可能千分之一都不到，也就是它只改变了一点点

但是就是这一点点它把解决了`margin`塌陷和`margin`合并的问题
##### 如何触发BFC
小结触发`BFC`方法：
```js

1. `float`属性为`left`/`right`

2. `overflow`为`hidden`/`scroll`/`auto`

3. `position`为`absolute`/`fixed`

4. `display`为`inline-block`/`table-cell`/`table-caption`

```
有些人会想，有这几多方式触发`BFC`，那用哪种方式好一些？

看具体情况，个人认为一个原则
> 针对需求，看哪个触发方式没有影响就用那个

下面来试试看
#### 解决margin塌陷的问题
如下通过`overflow: hidden`触发`bfc`
```css

div.father{ 

 width: 200px; 

 height: 200px; 

 background-color: rgb(219, 68, 101); 

 margin-left: 100px; 

 margin-top: 100px; 

 overflow: hidden; /* 触发bfc */ 

} 

div.father div.son{ 

 width: 100px; 

 height: 100px; 

 background-color: rgb(56, 248, 207); 

 margin-left: 50px; 

 margin-top: 50px; 

}

```
结果如下

![](https://s2.loli.net/2022/06/10/dBiHpZIuksYU3Rn.webp)

理想的结果

问题已经解决~
### margin合并
#### 什么是margin合并
还是来看个例子，把两个兄弟块元素，一个设置下外边距100px，一个设置上外边距100px，让两个元素相距200px。
```css

.one{ 

 background-color: pink; 

 height: 20px; 

 margin-bottom: 100px; 

} 

.two{ 

 background-color: purple; 

 height: 20px; 

 margin-top: 100px; 

}

```
结果如下

![](https://s2.loli.net/2022/06/10/IeBVrgUQ2XsFpYm.webp)

我们发现这两个元素之间，他们的`margin-bottom`和`margin-top`合并了，并且显示的是较大值

这种现象被称为`margin`合并，解决的方式也是通过触发`bfc`来解决
#### 解决margin合并
```css

.one { 

 background-color: pink; 

 height: 20px; 

 margin-bottom: 100px; 

} 

.wrap{ 

 /* 触发bfc */ 

 overflow: hidden; 

} 

.wrap .two { 

 background-color: purple; 

 height: 20px; 

 margin-top: 100px; 

}

```
结果如下：

![](https://s2.loli.net/2022/06/10/XbNyI9T8DQatEhO.webp)

解决问题！但是一般不这么解决，为什么？

因为`margin`合并和`margin`塌陷不一样

`margin`塌陷只添加了`CSS`，`margin`合并除了添加`CSS`，还修改了`HTML`结构

我们知道一般html结构是不能乱改动的，所以我们通过数学计算来解决这各`margin`合并的问题

比如上面的例子，我们只要设置前面元素的`margin-bottom`为`200px`或者后面元素的`margin-top`为`200px`即可

以上就是关于`margin`塌陷和`margin`合并的小结