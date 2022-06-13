## 手把手带你10分钟手撸一个简易的Markdown编辑器

### 前言

最近在项目中需要实现一个 **「markdown编辑器」** 的需求，并且是以`React`框架为开发基础的，类似掘金这样的：

![img](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091908292.png)

第一想法肯定是能用优秀的开源就一定用开源的，毕竟不能老是重复造轮子。

我找了很多 markdown 编辑器项目，但我一看全是基于`Vue`使用的，不太符合预期，逛了一下`github`，也没看到我满意的项目，所以干脆就自己实现一个。

### 需要实现的功能

我们自己实现的话，看看需要支持哪些功能，因为做一个初版的简易编辑器，所以功能实现得不会太多，但绝对够用：

-   markdown语法解析，并实时渲染
-   markdown主题css样式
-   代码块高亮展示
-   「编辑区」和「展示区」的页面同步滚动
-   编辑器工具栏中工具的实现

这里先放上我最终实现好了的效果图：

![最终效果图](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091910845.gif)

本文的代码在了 [Github 仓库](https://github.com/zero2one3/markdown-editor-reactjs)上。

[在线体验的地址](http://lpyexplore.gitee.io/taobao_staticweb/markdown-editor-reactjs/))。

### 具体实现

具体的实现也是按照我们上述列出来的功能的顺序来一一实现的。

> 说明：本文通过循序渐进的方式讲解，所以重复代码可能有点多。并且每一部分的注释是专门用于讲解该部分的代码的，所以在看每一部分功能代码时，只需要看注释部分就好~

#### 一、布局

```js
import React, {  } from 'react'  
  
export default function MarkdownEdit() {  
    return (  
        <div className="markdownEditConainer">  
            <textarea className="edit" />  
            <div className="show" />  
        </div>  
    )  
}  
```

css样式我就不一一列举了，整体就是左边是**「编辑区」**，右边是**「展示区」**，具体样式如下：

![布局图](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091913386.png)

#### 二、markdown语法解析

接下来就需要思考如何将 **「「编辑区」」** 输入的`markdown`语法解析成`html`标签并最终渲染在 **「「展示区」」**。

查找了一下目前比较优秀的`markdown`解析的开源库，常用的有三个，分别是`Marked`、`Showdown`、`markdown-it` ，并借鉴了一下其它大佬的想法，了解了一下这三个库的优缺点，对比如下：

| 库名 | 优点 | 缺点 |
| :----: | :----: | :----: |
| Marked | 性能好，正则解析（中文支持比较好） | 扩展性较差 |
| Showdown | 扩展性好、正则解析（中文支持好） | 性能较差 |
| markdown-it | 扩展性好、性能较好 | 逐字符解析（中文支持不好） |

刚开始我选择了`showdown`这个库，因为这个库使用起来特别方便，而且官方已经在库中提供了很多扩展功能，只需要配置一些字段即可。

但是后来我又分析了一波，还是选用了`markdown-it`，因为之后可能需要做更多的语法扩展，`showdown`的官方文档写的比较生硬，而且`markdown-it`使用的人也多，生态比较好。

虽然其官方没有支持很多扩展的语法，但是已经有很多基于`makrdown-it`的功能扩展插件了，最重要的是`markdown-it`的官方文档写得好啊（而且有中文文档）！

接下来写一下`markdown`语法解析的代码吧（其中步骤1、2、3表示的是markdown-it库的用法）。

```js
import React, { useState } from 'react'  
// 1. 引入markdown-it库  
import markdownIt from 'markdown-it'  
  
// 2. 生成实例对象  
const md = new markdownIt()  
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  // 存储解析后的html字符串  
  
    // 3. 解析markdown语法  
    const parse = (text: string) => setHtmlString(md.render(text));  
  
    return (  
        <div className="markdownEditConainer">  
            <textarea   
                className="edit"   
                onChange={(e) => parse(e.target.value)} // 编辑区内容每次修改就更新变量htmlString的值  
            />  
            <div   
                className="show"   
                dangerouslySetInnerHTML={{ __html: htmlString }} // 将html字符串解析成真正的html标签  
            />  
        </div>  
    )  
}  
```

对于将 **「html字符串」** 转化为 **「真正的html标签」** 的操作，我们借助了React提供的`dangerouslySetInnerHTML`属性，详细的使用可以看[React 官方文档](https://zh-hans.reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml)

此时一个简单的`markdown`语法解析功能就实现了，来看看效果

![markdown语法解析效果展示图](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091919798.png)

两边确实正在同步更新，但是.....看起来好像哪里不太对！

其实是没问题的，被解析好的 `html字符串` 每个标签都被附带上了特定的类名，只是现在我们引入任何的样式文件，例如下图:

![img](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091919652.png)


我们可以打印解析出来的`html字符串`看看是什么样的

```html
<h1 id="">大标题</h1>  
<blockquote>  
  <p>本文来自公众号：前端印象</p>  
</blockquote>  
<pre><code class="js language-js">let name = '零一'  
</code></pre>  
```

#### 三、markdown主题样式

接下来我们可以去网上找一些markdown的主题样式css文件，例如我用一个最简单`Github`主题的markdown样式。另外我还是很推荐[Typora Theme](https://theme.typora.io/)，上面有很多很多的markdown主题

因为我这个样式主题是有一个前缀id `write`（Typora上的大部分主题前缀也是`#write`），所以我们给展示区的标签加上该类id，并引入样式文件

```js
import React, { useState } from 'react'  
import './theme/github-theme.css'  // 引入github的markdown主题样式  
import markdownIt from 'markdown-it'  
  
const md = new markdownIt()  
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  
  
    const parse = (text: string) => setHtmlString(md.render(text));  
  
    return (  
        <div className="markdownEditConainer">  
            <textarea   
                className="edit"   
                onChange={(e) => parse(e.target.value)}   
            />  
            <div   
                className="show"  
                id="write"  // 新增write的ID名   
                dangerouslySetInnerHTML={{ __html: htmlString }}  
            />  
        </div>  
    )  
}  
```

再来看看加入样式后的渲染结果图

![带样式的markdown渲染效果图](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091922673.png)

#### 四、代码块高亮

markdown语法的解析已经完成了，并且也有对应的样式了，但是代码块好像还没有高亮样式

这块儿我们自己来从0到1的实现是不可能的，可以用现成的开源库 `highlight.js`，[highlight.js 官方文档](https://highlightjs.org/)，这个库能帮你做的就是检测**「代码块标签元素」**，并为其加上特定的类名。这里放上这个库的[API文档](https://highlightjs.readthedocs.io/en/latest/api.html#highlightauto-code-languagesubset)

`highlight.js` 默认是检测它所支持的所有语言的语法的，我们就不需要关心了，并且其提供了很多的代码高亮主题，我们可以在官网进行预览，如下图所示：

![](https://s2.loli.net/2022/06/10/fKdp2zJtAgX8Z1G.webp)

更大的好消息来了！`markdown-it`已经将`highlight.js`集成进去了，直接设定一些配置即可，并且我们需要先将该库下载下来。具体的可以看[markdown-it中文官网 - 高亮语法配置](https://markdown-it.docschina.org/#用法示例)

同时在目录`highlight.js/styles/`下有很多很多的主题，可以自行导入

接下来就来实现一下代码高亮的功能吧

```js
import React, { useState, useEffect } from 'react'  
import markdownIt from 'markdown-it'  
import './theme/github-theme.css'  
import hljs from 'highlight.js'  // 引入highlight.js库  
import 'highlight.js/styles/github.css'  // 引入github风格的代码高亮样式  
  
const md = new markdownIt({  
    // 设置代码高亮的配置  
    highlight: function (code, language) {        
        if (language && hljs.getLanguage(language)) {  
          try {  
            return `<pre><code class="hljs language-${language}">` +  
                   hljs.highlight(code, { language  }).value +  
                   '</code></pre>';  
          } catch (__) {}  
        }  
      
        return '<pre class="hljs"><code>' + md.utils.escapeHtml(code) + '</code></pre>';  
    }  
})  
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  
  
    const parse = (text: string) => setHtmlString(md.render(text));  
  
    return (  
        <div className="markdownEditConainer">  
            <textarea   
                className="edit"   
                onChange={(e) => parse(e.target.value)}   
            />  
            <div   
                className="show"  
                id="write"  
                dangerouslySetInnerHTML={{ __html: htmlString }}  
            />  
        </div>  
    )  
}  
```

来看一下代码高亮的效果图：

![代码高亮效果图](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091926506.png)



#### 五、同步滚动

markdown编辑器还有一个重要的功能就是在我们滚动一个区域的内容时，另一块区域也跟着同步的滚动，这样才方便查看

接下来我们来实现一下，我会将我实现时踩的坑也一并列出来，让大家也印象深刻点，免得以后也犯同样的错误

刚开始主要实现思路就是当滚动其中一块区域时，计算滚动比例（`scrollTop / scrollHeight`），然后使另一块区域当前的滚动距离占总滚动高度的比例等于该滚动比例

```js
import React, { useState, useRef, useEffect } from 'react'  
import markdownIt from 'markdown-it'  
import './theme/github-theme.css'    
import hljs from 'highlight.js'  
import 'highlight.js/styles/github.css'   
  
const md = new markdownIt({  
    highlight: function (code, language) {        
        if (language && hljs.getLanguage(language)) {  
          try {  
            return `<pre><code class="hljs language-${language}">` +  
                   hljs.highlight(code, { language  }).value +  
                   '</code></pre>';  
          } catch (__) {}  
        }  
      
        return '<pre class="hljs"><code>' + md.utils.escapeHtml(code) + '</code></pre>';  
    }  
})  
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  
    const edit = useRef(null)  // 编辑区元素  
    const show = useRef(null)  // 展示区元素  
  
    const parse = (text: string) => setHtmlString(md.render(text));  
  
    // 处理区域的滚动事件  
    const handleScroll = (block: number, event) => {  
        let { scrollHeight, scrollTop } = event.target  
        let scale = scrollTop / scrollHeight  // 滚动比例  
  
        // 当前滚动的是编辑区  
        if(block === 1) {  
            // 改变展示区的滚动距离  
            let { scrollHeight } = show.current  
            show.current.scrollTop = scrollHeight * scale  
        } else if(block === 2) {  // 当前滚动的是展示区  
            // 改变编辑区的滚动距离  
            let { scrollHeight } = edit.current  
            edit.current.scrollTop = scrollHeight * scale  
        }  
    }  
  
    return (  
        <div className="markdownEditConainer">  
            <textarea   
                className="edit"   
                ref={edit}  
                onScroll={(e) => handleScroll(1, e)}  
                onChange={(e) => parse(e.target.value)}   
            />  
            <div   
                className="show"  
                id="write"  
                ref={show}  
                onScroll={(e) => handleScroll(2, e)}  
                dangerouslySetInnerHTML={{ __html: htmlString }}  
            />  
        </div>  
    )  
}  
```

这是我做的时候的第一版，确实是实现了两块区域的同步滚动，但是存在两个bug，来看看是哪两个

**「bug1：」**

这是一个很致命的bug，先埋个伏笔，先来看效果：

![初版同步滚动效果图](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091926592.gif)

同步滚动的效果实现了，但能很明显得看到，当我手动滚动完以后停止了任何操作，但是两个区域仍然在不停的滚动，这是为什么呢？

排查了一下代码，发现 `handleScroll` 这个方法会无限触发，假设当我们手动滚动一次编辑区后会触发其 `scroll`方法，即会调用 `handleScroll` 方法，然后会去改变「展示区」的滚动距离，此时又会触发展示区的 `scroll`方法，即调用 `handleScroll` 方法，然后会去改变「编辑区」的滚动距离 .... 就这样一直循环往复，才会出现图中的bug

后来我想了个比较简单的解决办法，就是用一个变量记住你当前手动触发的是哪个区域的滚动，这样就可以在 `handleScroll` 方法里区分此次滚动是被动触发的还是主动触发的了

```js
import React, { useState, useRef, useEffect } from 'react'  
import markdownIt from 'markdown-it'  
import './theme/github-theme.css'    
import hljs from 'highlight.js'  
import 'highlight.js/styles/github.css'  
  
const md = new markdownIt({  
    highlight: function (code, language) {        
        if (language && hljs.getLanguage(language)) {  
          try {  
            return `<pre><code class="hljs language-${language}">` +  
                   hljs.highlight(code, { language  }).value +  
                   '</code></pre>';  
          } catch (__) {}  
        }  
      
        return '<pre class="hljs"><code>' + md.utils.escapeHtml(code) + '</code></pre>';  
    }  
})  
  
let scrolling: 0 | 1 | 2 = 0  // 0: none; 1: 编辑区主动触发滚动; 2: 展示区主动触发滚动  
let scrollTimer;  // 结束滚动的定时器  
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  
    const edit = useRef(null)   
    const show = useRef(null)    
  
    const parse = (text: string) => setHtmlString(md.render(text));  
  
    const handleScroll = (block: number, event) => {  
        let { scrollHeight, scrollTop } = event.target  
        let scale = scrollTop / scrollHeight    
  
        if(block === 1) {  
            if(scrolling === 0) scrolling = 1;  // 记录主动触发滚动的区域  
            if(scrolling === 2) return;    // 当前是「展示区」主动触发的滚动，因此不需要再驱动展示区去滚动  
  
            driveScroll(scale, showRef.current)  // 驱动「展示区」的滚动  
        } else if(block === 2) {    
            if(scrolling === 0) scrolling = 2;  
            if(scrolling === 1) return;    // 当前是「编辑区」主动触发的滚动，因此不需要再驱动编辑区去滚动  
  
            driveScroll(scale, editRef.current)  
        }  
    }  
  
    // 驱动一个元素进行滚动  
    const driveScroll = (scale: number, el: HTMLElement) => {  
        let { scrollHeight } = el  
        el.scrollTop = scrollHeight * scale  
  
        if(scrollTimer) clearTimeout(scrollTimer);  
        scrollTimer = setTimeout(() => {  
            scrolling = 0    // 在滚动结束后，将scrolling设为0，表示滚动结束  
            clearTimeout(scrollTimer)  
        }, 200)  
    }  
  
    return (  
        <div className="markdownEditConainer">  
            <textarea   
                className="edit"   
                ref={edit}  
                onScroll={(e) => handleScroll(1, e)}  
                onChange={(e) => parse(e.target.value)}   
            />  
            <div   
                className="show"  
                id="write"  
                ref={show}  
                onScroll={(e) => handleScroll(2, e)}  
                dangerouslySetInnerHTML={{ __html: htmlString }}  
            />  
        </div>  
    )  
}  
```

这样就解决了上述的bug了，同步滚动也算很不错得实现了，现在的效果就跟文章开头展示的图片里效果一样了

**「bug2：」**

这里还存在一个很小的问题，也不算是bug，应该算是设计上的思路问题，那就是两个区域其实还没完完全全实现同步滚动。先来看看原先的设计思想

![img](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091927808.png)

编辑区和展示区的可视高度是一样的，但一般编辑区的内容经过markdown渲染后，总的滚动高度是会高于编辑区总的滚动高度的，所以我们无法仅凭`scrollTop`和`scrollHeight`使得两个区域同步滚动，比较晦涩，用具体的数据来看一下

| 属性 | 编辑区 | 展示区 |
| :----: | :----: | :----: |
| clientHeight | 300 | 300 |
| scrollHeight | 500 | 600 |

假设我们现在滚动编辑区到最底部，那么此时「编辑区」的 `scrollTop` 应为 `scrollHeight - clientHeight = 500 - 300 = 200`，按照我们原本计算滚动比例的方式得出 `scale = scrollTop / scrollHeight = 200 / 500 = 0.4`，那么「展示区」同步滚动后，`scrollTop = scale * scrollHeight = 0.4 * 600 = 240 < 600 - 300 = 300`。

但事实就是编辑区滚动到最底部了，而展示区还没有，显然不是我们要的效果

换一种思路，我们在计算滚动比例时，应计算的是当前的 `scrollTop` 占 `scrollTop`最大值的比例，这样就能实现同步滚动了，仍然用刚才那个例子来看：此时编辑区滚动到最底部，那么`scale`应为 `scrollTop / (scrollHeight - clientHeight) = 200 / (500 - 300) = 100%`，表示编辑区滚动到最底部了，那么在展示区同步滚动时，他的 `scrollTop` 就变成了 `scale * (scrollHeight - clientHeight) = 100% * (600 - 300) = 300`，此时的展示区也同步滚动到了最底部，这样就实现了真正的同步滚动了

来看一下改进后的代码

```js
import React, { useState, useRef, useEffect } from 'react'  
import markdownIt from 'markdown-it'  
import './theme/github-theme.css'    
import hljs from 'highlight.js'  
import 'highlight.js/styles/github.css'  
  
const md = new markdownIt({  
    highlight: function (code, language) {        
        if (language && hljs.getLanguage(language)) {  
          try {  
            return `<pre><code class="hljs language-${language}">` +  
                   hljs.highlight(code, { language  }).value +  
                   '</code></pre>';  
          } catch (__) {}  
        }  
      
        return '<pre class="hljs"><code>' + md.utils.escapeHtml(code) + '</code></pre>';  
    }  
})  
  
let scrolling: 0 | 1 | 2 = 0    
let scrollTimer;    
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  
    const edit = useRef(null)   
    const show = useRef(null)    
  
    const parse = (text: string) => setHtmlString(md.render(text));  
  
    const handleScroll = (block: number, event) => {  
        let { scrollHeight, scrollTop, clientHeight } = event.target  
        let scale = scrollTop / (scrollHeight - clientHeight)  // 改进后的计算滚动比例的方法  
  
        if(block === 1) {  
            if(scrolling === 0) scrolling = 1;    
            if(scrolling === 2) return;      
  
            driveScroll(scale, showRef.current)    
        } else if(block === 2) {    
            if(scrolling === 0) scrolling = 2;  
            if(scrolling === 1) return;      
  
            driveScroll(scale, editRef.current)  
        }  
    }  
  
    // 驱动一个元素进行滚动  
    const driveScroll = (scale: number, el: HTMLElement) => {  
        let { scrollHeight, clientHeight } = el  
        el.scrollTop = (scrollHeight - clientHeight) * scale  // scrollTop的同比例滚动  
  
        if(scrollTimer) clearTimeout(scrollTimer);  
        scrollTimer = setTimeout(() => {  
            scrolling = 0     
            clearTimeout(scrollTimer)  
        }, 200)  
    }  
  
    return (  
        <div className="markdownEditConainer">  
            <textarea   
                className="edit"   
                ref={edit}  
                onScroll={(e) => handleScroll(1, e)}  
                onChange={(e) => parse(e.target.value)}   
            />  
            <div   
                className="show"  
                id="write"  
                ref={show}  
                onScroll={(e) => handleScroll(2, e)}  
                dangerouslySetInnerHTML={{ __html: htmlString }}  
            />  
        </div>  
    )  
}  
```

两个bug都已经解决了，同步滚动的功能也算完美实现啦。但对于同步滚动这个功能，其实有两种概念，

- 一种是两个区域在滚动高度上保持同步滚动
- 一种是右侧的展示区域对应左侧的编辑区的内容进行滚动。

我们现在实现的是前者，后者可以后续作为新功能实现一下~

#### 六、工具栏

最后我们就再实现一下编辑器的工具栏部分的工具（加粗、斜体、有序列表等等），因为这几个工具的实现思路都一致，我们就拿 **「「加粗」」** 这个工具举例子，其余的就可以模仿着写出来了

加粗工具的实现思路：

-   光标是否选中文字？
-   是。将选中文字的两侧加上`**`
-   否。在光标所在处添加文字`**加粗文字**`

动图效果演示：

![加粗工具动图演示](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109091930912.gif)

```js
import React, { useState, useRef, useEffect } from 'react'  
import markdownIt from 'markdown-it'  
import './theme/github-theme.css'    
import hljs from 'highlight.js'  
import 'highlight.js/styles/github.css'  
  
const md = new markdownIt({  
    highlight: function (code, language) {        
        if (language && hljs.getLanguage(language)) {  
          try {  
            return `<pre><code class="hljs language-${language}">` +  
                   hljs.highlight(code, { language  }).value +  
                   '</code></pre>';  
          } catch (__) {}  
        }  
      
        return '<pre class="hljs"><code>' + md.utils.escapeHtml(code) + '</code></pre>';  
    }  
})  
  
let scrolling: 0 | 1 | 2 = 0    
let scrollTimer;    
  
export default function MarkdownEdit() {  
    const [htmlString, setHtmlString] = useState('')  
    const [value, setValue] = useState('')   // 编辑区的文字内容  
    const edit = useRef(null)   
    const show = useRef(null)    
  
    const handleScroll = (block: number, event) => {  
        let { scrollHeight, scrollTop, clientHeight } = event.target  
        let scale = scrollTop / (scrollHeight - clientHeight)    
  
        if(block === 1) {  
            if(scrolling === 0) scrolling = 1;    
            if(scrolling === 2) return;      
  
            driveScroll(scale, showRef.current)    
        } else if(block === 2) {    
            if(scrolling === 0) scrolling = 2;  
            if(scrolling === 1) return;      
  
            driveScroll(scale, editRef.current)  
        }  
    }  
  
    // 驱动一个元素进行滚动  
    const driveScroll = (scale: number, el: HTMLElement) => {  
        let { scrollHeight, clientHeight } = el  
        el.scrollTop = (scrollHeight - clientHeight) * scale    
  
        if(scrollTimer) clearTimeout(scrollTimer);  
        scrollTimer = setTimeout(() => {  
            scrolling = 0     
            clearTimeout(scrollTimer)  
        }, 200)  
    }  
  
    // 加粗工具  
    const addBlod = () => {  
        // 获取编辑区光标的位置。未选中文字时：selectionStart === selectionEnd ；选中文字时：selectionStart < selectionEnd  
        let { selectionStart, selectionEnd } = edit.current  
        let newValue = selectionStart === selectionEnd  
                        ? value.slice(0, start) + '**加粗文字**' + value.slice(end)  
                        : value.slice(0, start) + '**' + value.slice(start, end) + '**' + value.slice(end)  
        setValue(newValue)  
    }  
  
    useEffect(() => {  
        // 编辑区内容改变，更新value的值，并同步渲染  
        setHtmlString(md.render(value))  
    }, [value])  
  
    return (  
        <div className="markdownEditConainer">  
            <button onClick={addBlod}>加粗</button>   {/* 假设一个加粗的按钮 */}  
            <textarea   
                className="edit"   
                ref={edit}  
                onScroll={(e) => handleScroll(1, e)}  
                onChange={(e) => setValue(e.target.value)}   // 直接修改value的值，useEffect会同步渲染展示区的内容  
                value={value}  
            />  
            <div   
                className="show"  
                id="write"  
                ref={show}  
                onScroll={(e) => handleScroll(2, e)}  
                dangerouslySetInnerHTML={{ __html: htmlString }}  
            />  
        </div>  
    )  
}  
```

借助这样的思路，就可以完成其它各种工具的实现了。

在我已经发布的[markdown-editor-reactjs](https://github.com/zero2one3/markdown-editor-reactjs)中，已经完成了其它工具的实现，想要看代码的可以去源码里看

#### 七、补充

为了保证包的体积足够小，我将**「第三方依赖库」**、**「markdown主题」**、**「代码高亮主题」**都通过外链的形式导入了

#### 八、最后

一个简易版的markdown编辑器就实现了，大家可以手动尝试实现一下。后续我也会继续发一些教程，对这个编辑器的功能进行扩展

代码在 [Github 仓库](https://github.com/zero2one3/markdown-editor-reactjs)

后续扩展一下功能，并作为一个完整的组件发布到npm给大家使用，希望大家多多支持~

> 本文来自 微信公众号：IQ前端，[原文地址](https://mp.weixin.qq.com/s/TbWvq-ODKUJuPs6B0w_gFw)