# CSS 代码重构与优化之路

> *** 学习进阶方式 💪**
> 
> **基础知识要夯实；**
> 
> **原理源码要深入；**
> 
> **深度广度要扩展；**

简短的概括：

1､发现问题：  

**项目中css代码痛点：**写CSS的同学们往往会体会到，随着项目规模的增加，项目中的CSS代码也会越来越多，如果没有及时对CSS代码进行维护，CSS代码不断会越来越多。你不知道修改这行代码会有什么影响，所以如果有修改或增加新功能时，开发人员往往不敢去删除旧的冗余的代码，而保险地增加新代码，最终的坏处就是项目中的CSS会越来越多，最终陷入无底洞。

2､解决问题：

**CSS代码重构：**我们写CSS代码时，不仅仅只是完成页面设计的效果，还应该让CSS代码易于管理，维护。我们对CSS代码重构主要有两个目的：提高代码性能，提高代码的可维护性。
  
---

**一、如何提高 CSS 性能**  

---
 
#### **一､CSS是如何工作的？**

##### **1､CSS阻止渲染**

当一个页面有CSS可用时，无论是内联还是外部样式表，浏览器都会延迟渲染，直到CSS被解析。这是因为没有CSS的页面通常是不可用的。

##### **2､CSS可以阻止HTML的解析**

尽管浏览器在完成CSS解析之前不会显示内容，但它会处理HTML的其余部分。然而脚本会阻止解析器，除非它们被标记为defer或async。一个脚本有可能操纵页面和其余代码，所以浏览器必须注意该脚本的执行时间。

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110042055819.webp)

屏蔽脚本的解析器：脚本如何屏蔽HTML解析。

因为脚本可以影响应用到页面的样式，如果浏览器仍在处理一些CSS，它就会等到处理完毕再运行脚本。因为在脚本运行之前不会继续解析文档，这意味着CSS不再只是阻止渲染--取决于文档中外部样式表和脚本的顺序，也可能停止HTML解析。

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110042056625.webp)

解析器阻塞CSS：CSS如何阻塞HTML解析。  

为了避免阻塞解析，请尽快交付CSS，并以最佳顺序安排你的资源。

**二､注意CSS的大小**

**1､压缩和最小化CSS**

压缩文件可以显著提高速度，服务器和客户端交互中使用最广泛的压缩格式是Gzip。

最小化是去除空白和任何不必要的代码的过程。输出的是一个更小但完全有效的代码文件，浏览器可以解析，这将为你节省一些字节。Terser是一个流行的JavaScript压缩工具，如果你使用webpack，v4包含一个插件来创建minified的构建文件。

**2､删除未使用的CSS**

去除未使用的CSS通常是手工操作。主要的挑战在于它有多么复杂。我们必须在所有可能的状态下，在所有可能的设备上仔细审核整个网站（以覆盖媒体查询），并执行所有可能改变样式的JavaScript功能。UnusedCSS和PurifyCSS是流行的工具，可以帮助查明不必要的样式，但我们应该配合仔细的视觉回归测试。

在这里，使用CSS-in-JS的显著优势：每个组件内渲染的样式都是只需要CSS。在CSS-in-JS中加快CSS的秘诀是将CSS内联到页面中，或者将其提取到外部CSS文件中。将CSS发送到一个JavaScript文件中会导致它的解析和缓慢计算。

---
**二、CSS的设计模式/架构**  

---


最近通用的CSS设计思想：OOCSS、SMACSS、BEMCSS、METACSS。

**一、OOCSS**

**1､OOCSS定义**

OOCSS(Object Oriented CSS)是面向对象css，旨在编写高可复用、低耦合和高扩展的CSS代码。

OOCSS是以面向对象的思想去定义样式，将抽象和实现分离，抽离公共代码。从而使代码 重用性，可维护性和可扩展性更好的书写方法。

  

**2､设计的主要规范**

1) 减少对 HTML 结构的依赖（分离结构和主题）

2) 增加样式的复用性（分离容器和内容）

在 OOCSS 的观念中，强调重复使用 class，而应该避免使用 id 作为 CSS 的选择器。OOCSS追求元件的复用，其class命名更为抽象，一般不体现具体事物，而注重表现层的抽取。

**3､项目实例：**

OOCSS鼓励我们应该思考在不同元素中哪些样式是通用的，然后将这些通用的样式从模块、组件、对象等中抽离出来，使其能在任何地方能够复用，而不依赖于某个特定的容器。

```css
.title {  
		font-family: Arial, Helvetica, sans-serif; 
		font-size: 2em;  
		line-height: 1;  
		color: #777;  
		text-shadow: rgba(0, 0, 0, .3) 3px 3px 6px;
}
.header .title {  
		font-size: 1.5em;  
		text-shadow: rgba(0, 0, 0, .3) 2px 2px 4px;
}
```

**4､OOCSS的优缺点**

优点：

1) css代码减少，降低工作量。

2) 样式重复利用，代码简洁，便于维护。

3) 代码少，加载速度快。

4) 能轻松构造新的页面布局，或制作新的页面风格

缺点：

1) 适用于大型网站项目（重复组件，样式多），小型项目优势不明显。

2) 需要熟练运用，因为特定要求（强调重复使用类选择器，避免使用id选择器）如果运用不得当，反而可能会造成后续维护困难，所以最好写上注释。

二、SMACSS

**1､smacss定义**

smacss通过一个灵活的思维过程来检查你的设计过程和方式是否符合你的架构。

**2､设计的主要规范有三点：**

1) Categorizing CSS Rules（为css分类）

2) Naming Rules（命名规范）

3) Minimizing the Depth of Applicability（最小化适配深度）

详细说明：

1､Categorizing CSS Rules css分类

这一点是SMACSS的核心。SMACSS认为css有5个类别，分别是：

1 Base

2 Layout

3 Module

4 State

5 Theme or Skin

2､Naming Rules 命名规范

按照前面5种的划分:

Base Rules(Pass)

Layout Rules用l-或layout-这样的前缀，例如：.l-header、.l-sidebar。

Module Rules用模块本身的命名，例如图文排列的.media、.media-image。

State Rules用is-前缀，例如：.is-active、.is-hidden。

Theme Rules如果作为单独class，用theme-前缀，例如.theme-a-background、.theme-a-shadow。
  
3､Minimizing the Depth of Applicability 最小适配深度原则，

简单的例子：

```css
/* depth 1 */
.sidebar ul h3 { }
/* depth 2 */
.sub-title { }
```

两段css的区别在于html和css的耦合度(这一点上和OOCSS的分离容器和内容的原则不谋而合)。可以想到，由于上面的样式规则使用了继承选择符，因此对于html的结构实际是有一定依赖的。如果html发生重构，就有可能不再具有这些样式。对应的，下面的样式规则只有一个选择符，因此不依赖于特定html结构，只要为元素添加class，就可以获得对应样式。

当然，继承选择符是有用的，它可以减少因相同命名引发的样式冲突（常发生于多人协作开发）。但是，我们不应过度使用，在不造成样式冲突的允许范围之内，尽可能使用短的、不限定html结构的选择符。这就是SMACSS的最小化适配深度的意义。
 
## **三、BEMCSS**

**1､BEM定义**

BEM的意思就是块（block）、元素（element）、修饰符（modifier）,是由Yandex团队提出的一种前端命名方法论。这种巧妙的命名方法让你的CSS类对其他开发者来说更加透明而且更有意义。BEM命名约定更加严格，而且包含更多的信息，它们用于一个团队开发一个耗时的大项目。

#### **2､为什么使用BEM**

1）更语意化，可读性更强

通过双下划线(__), 双横杠(--) 等符号代码维护者可以轻松理解每一部分的意义，更强的可读性往往意味着更低的维护成本。
  
2）模块化，减少层叠带来的样式覆盖的问题

Block是完全独立的存在，其内部的element/modifier的样式都在这个block的命名空间下书写的，所以不会收到其他外部样式的影响，不存在样式覆盖的问题。

3）增强样式的重用性

就像js组合不同的组件得到更复杂的组件一样，我们也可以通过组合不同的block得到更复杂的样式，例如使用.b-btn, .b-input来组合一个简单的form样式，从而提高代码的可复用性，从另一方面讲也是降低了维护成本。
  
4）更容易做项目迁移

因为block样式是相对独立的，如果在其他项目有需要，我们完全可以讲某个单独的block相关的样式应用到其他项目中。  

*CSS的设计模式/架构总结

1) oocss着重可复用，把每一个dom节点当成一个对象，是css返璞归真的思想；

2) smacss覆盖了所有的细节点；

3) bemcss着重css的命名和语义化；

---

**三、迎接原子化 CSS 时代**  

---

随着 Facebook 和 Twitter 最近的产品部署，我认为一个新的趋势正在缓慢增长：**Atomic CSS-in-JS**。

## **1､什么是原子 CSS**

你可能听说过各种 CSS 方法，如 BEM, OOCSS…

```html
<button class="button button--state-danger">Danger button</button>
```

现在，人们真的很喜欢 **Tailwind CSS** 和它的 实用工具优先（utility-first） 的概念。这与 Functional CSS 和 Tachyon 这个库的理念非常接近。

```html
<button  class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">  Button</button>
```

用海量的实用工具类（utility classes）组成的样式表，让我们可以在网页里大显身手。

原子 CSS 就像是实用工具优先（utility-first）CSS 的一个极端版本: 所有 CSS 类都有一个唯一的 CSS 规则。原子 CSS 最初是由 Thierry Koblentz (Yahoo!)在 2013 年挑战 CSS 最佳实践时使用的。

```css
/* 原子 CSS */
.bw-2x {
		border-width: 2px;
}
.bss {
		border-style: solid;
}
.sans {
	font-style: sans-serif;
}
.p-1x {
		padding: 10px;
}
/* 不是原子 CSS 因为这个类包含了两个规则 */
.p-1x-sans {
		padding: 10px;
		font-style: sans-serif;
}
```

使用实用工具/原子 CSS，我们可以把结构层和表示层结合起来：当我们需要改变按钮颜色时，我们直接修改 HTML，而不是 CSS！

---

**四、项目实践-代码**  

---

1､超出省略号

```css
// ****** 超出省略号
@mixin ellipsis($line) {
		display: -webkit-box;
		-webkit-box-orient:vertical;
		overflow: hidden;
		-webkit-line-clamp: $line;
}

.tit-name {
		@include ellipsis(1);
		color: #333;
}
```

2､继承 @extend

```css
h1{
		border: 4px solid #ff9aa9;
}

.speaker{ 
		@extend h1;
		border-width: 2px;
}
```

3､函数 px2rem

```css
@function px2rem ($px) { 
		$rem : 37.5px;
		@return ($px / $rem) + rem;
}

.hello { 
		width: px2rem (100px);  
		height: px2rem (100px); 
		&.b {  
				width: px2rem ( 50px);
				height: px2rem (50px);  
	}
}
```

4､表单输入框颜色设置

```css
// 输入框设置input { 
		font-size: 14px; 
		color: #2c3e50;
}

input::-webkit-input-placeholder {
		color: #c0c4cc;
}

// 多行文本输入框设置
textarea {
		font-size: 14px;
		color: #2c3e50;
}

textarea::-webkit-input-placeholder { 
		color: #c0c4cc; 
}
```

5､自定义滑动条样式

```css
/* 定义整个滑动条的宽度和高度 */
.content-nav::-webkit-scrollbar {
		width: 8px;
}

/* 定义滑块的样式 */
.content-nav::-webkit-scrollbar-thumb { 
		box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
		background: #34495e;
}

/* 定义滑块里面的轨道 */
.content-nav::-webkit-scrollbar-track {
		box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);  
		background: #99a3ae;
}
```

6､弹出框表单设置  

```css
.dialog-from { 
		// input 输入框  
		.el-input{
				width: 200px;  
		}  
	
		// select 下拉菜单  
		.el-select{
				width: 200px;
		}  
	
		// textarea 菜单  
		.el-textarea{
				width: 200px; 
		}  
	
		// radio 菜单  
		.el-radio-group{   
				width: 200px;  
		}
}
```
