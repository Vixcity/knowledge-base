## 准备条件

这期的源码比较简单，我们直接来看

首先上一个[github地址](https://github.com/npm/validate-npm-package-name)

然后我们可以直接看他的readme文档

![readme](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251855411.png)

这一段非常简洁清晰的表明了这个包的用途

老样子，我们克隆一下，然后安装一下依赖，这里就不过多赘述

今天呢，我们就不从他的测试入手了，我们从他的example入手

![example](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251857160.png)

我们新建一个example文件

在这个文件中输入以下js代码

```js
var validate = require("./index") 

validate("some-package")
validate("example.com")
validate("under_score")
validate("123numeric")
validate("@npm/thingy")
validate("@jane/foo.js")
```

然后按照我[上期的方法](http://vixcity.gitee.io/my-gitee-blog/2021/10/23/%E3%80%90%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB%E3%80%91update-notifier%20%E6%9B%B4%E6%96%B0%20CLI%20%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E7%9A%84%E9%80%9A%E7%9F%A5-%E8%AF%A6%E8%A7%A3/)进行调试即可食用

## 开始调试

我们打上对应断点，开始调试

![对应断点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251912189.png)

他这里先是拿了两个数组

分别是警告和错误

![警告和错误](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251915810.png)

他们两个的作用这里我盲猜一波是来收集的😆

首先判断了一手传入的字符串是不是null或者undefined

![首先](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251918174.png)

在去判断他是不是字符串

这三个相当于来说这这个的根基，如果你不符合调条件的话

直接不让你走了

接下来就是看你传入的是不是空字符串，是不是点开头的，是不是下划线开头的

符不符合命名规范，不符合的话，给你收集一个错误

在看看字符串是不是头尾有空格

接下来看看你取的名字是不是在黑名单当中，即是否含有关键词

这一块作者给了个备注

```js
// No funny business

// 无聊的业务
```

是个有趣的作者没跑了哈哈哈

接下来，作者在去判断了一下是否和NodeJs里面的内置函数重名了

如果重名了，那么就给你加到警告里面

接下来就是各种检查，这里就简单的过一下

![检查](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251934273.png)

接下来就是检查你的有作用域的包名

接着给你的对应的错误的地方添加一个错误

![包名](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110251937474.png)

最后一个函数就是收集依赖的函数

把你的错误收集起来

如果没有错的话，那么就删除对应的错误数组并返回

## 总结
这是一段非常简单的源码，我们可以通过这个来练习重构

如果是我们写的话，可以先把done函数放在各种判断里面，之后再给他抽离出来，单独封装成一个函数