# co.js - 让异步代码同步化
在阅读源码之前，首先我们要了解一下这个库是干啥的

通过[百度](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&tn=baidu&wd=co.js&oq=cojs&rsv_pq=f9486df7000c8aee&rsv_t=b426DY1HEwDoDkGG334uAs8aSkbAy9txsYVgRx7ryoUclQ9wk6b90fm4oXM&rqlang=cn&rsv_enter=1&rsv_dl=tb&rsv_sug3=2&rsv_sug1=2&rsv_sug7=100&rsv_n=2&rsv_sug2=0&rsv_btype=t&inputT=950&rsv_sug4=22110)，我们可以捕捉到以下这么几个关键词

![关键词](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151512508.jpg)

1. promise对象
2. 让异步代码同步化
3. 异步
4. KOA框架的核心库
5. ES6 Generator
6. 同步

那么我们就可以大胆的先猜测以下几点

1. 他是个可以让异步代码同步化的这么一个库
2. 他让异步变成同步的原理是通过 ES6 的 Generator 这个新特性来解决的
3. 他让写法变成了同步的写法，解决了异步回调的问题
4. 和 promise 应该也有联系
5. 他是KOA框架的核心库

OK，接下来我们就带着这么个问题来他的 [github](https://github.com/tj/co) 看一下他的介绍

为了方便观看，我这里把英文的 github 转成了中文

![简介](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151528440.jpg)

> nodejs 和浏览器的基于生成器的控制流优点，使用承诺，让你以一种很好的方式编写非阻塞代码。

因为第四期和第五期的内容都是和 koa 有关的，所以我们先了解下 koa 吧

老样子，我们先百度，发现有四个内容很吸引人啊

![百度](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151539372.jpg)

第一个和第三个其中有一个肯定是[官网](https://www.koajs.com.cn/)

我这里就直接贴上来了，第二个是广告，我们可以不用管它

第四个是我们廖雪峰大大的[博客](https://www.liaoxuefeng.com/wiki/1022910821149312/1099752344192192)

OK，那我们先去官网上看看吧

我们的主要核心是 co 所以我们大体上了解一下 koa 是干啥的就行

![啥是koa](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151551746.jpg)

![异步版的express](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151552116.jpg)

1. koa 是由 express 原班人马打造的
2. 类似于其他中间件

我们可以猜测，koa是一个异步中间件系统

异步版本的 express

因为本身是异步的，所以如果需要执行顺序的话就会出现奇奇怪怪的 bug

那么 co 就给我们提供了这个解决方案

```bash
# 克隆我的这个仓库
git clone https://github.com/lxchuan12/koa-analysis.git
# chrome 调试：
# 全局安装 http-server
npm i -g http-server  
hs koa/examples/  
# 可以指定端口 -p 3001  
# hs -p 3001 koa/examples/  
# 浏览器中打开  
# 然后在浏览器中打开localhost:8080，开心的把代码调试起来
```
出现这个↓，就代表调试成功了

![调试成功](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151617685.jpg)

examples 文件夹的简单介绍

-   `middleware`文件夹是用来`vscode`调试整体流程的。  
-   `simpleKoa` 文件夹是`koa`简化版，为了调试`koa-compose`洋葱模型如何串联起来各个中间件的。  
-   `koa-convert`文件夹是用来调试`koa-convert`和`co`源码的。  
-   `co-generator`文件夹是模拟实现`co`的示例代码。
