## 前言
Hello，大家好，这里是V同学

今天我们又来看源码了

今天若川大大没有写源码阅读的掘金文章

正好，可以让我们一试身手

考验一下我们之前跟随着川大的源码所锻炼的源码阅读能力

今天我们要看的是 update-notifier 的源码

老样子，在我们阅读源码之前，先分析一手，看看这个到底是干嘛用的吧

## 分析
这个是我们本期源码阅读的[代码仓库](https://github.com/yeoman/update-notifier)

大家可以先克隆下来，我们且慢慢分析

我们可以用[[5w1h分析法]]来试着去分析一下

### 5w1h

那么什么是5w1h呢，简单来说就是从

- 原因（Why）
- 对象（What）
- 地点（Where）
- 时间（When）
- 人员（Who）
- 方法（How）

这六个纬度去思考

那我们就一个一个来

### What

首先，我们要来看看本期源码是什么

通过度娘，我们可以大致的了解到，我们本期要看的源码

似乎是一个和更新有关的工具，可以更新你的npm包和cli应用程序

![百度](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110211032380.jpg)

我们在看一下仓库的readme

![readme](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110211034793.png)

现在就非常的清晰明了了，这是一个以非入侵的方式通知你更新的这么一个工具

### Why

那么我们为什么要读它的源码呢

有一下几点
```bash
1. 美化终端输出信息，描绘边框。
2. 我们可以学会怎么去开启子进程
3.  给一个node方法作为传参的几种方式
4.  通过configstore这个库我们能够持久化存储一些信息
5.  通过latest-version获取对应pkg包的最新版本信息
```

### Who
谁应该去读他的源码

以下几类人适合去读他的源码，当然也适合来看看我的源码阅读的文章

```bash
1. 源码爱好者
2. 学习爱好者
3. 希望能学到东西的人
4. V同学的粉丝😜
……
```

在我的源码阅读的文章里面，我会一步一步的手摸手教你怎么去拆分阅读以及调试源码

### Where

在哪里学的话，这个大家可以视实际情况而定

### When

什么时候学的也取决于你的最佳学习时间，每个人的最佳的专注和学习时间不一样

具体的话视个人情况而定

### How
那么我们就到了最关键的一点

怎么去读

没有度过源码的同学请放心

跟着V同学走，手摸手，包你药到命除🤪

额不不不，是药到病除

## 依赖

刚刚，我们把仓库的地址克隆下来了，那么接下来我们就安装一下依赖吧

进入到对应的仓库地址

输入一下命令

```bash 
npm i 

或者

yarn
```

## 测试

测试非常的重要，他可以让我们非常清晰的了解

一个框架的功能以及作者写这个测试的意图，所以我们看源码，可以先看看测试

我这边用的是 vscode

找到 package.json 文件，点击`调试`进入测试

![进入测试](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110211055408.png)

有人说了，我用的不是vscode怎么办呢

其实也简单的

首先还是先问问百度，你是用的编译器是否有对应的测试插件

没有的话，可以在编译器的控制台输入测试中的命令

```bash
npm run test

或者

yarn test
```

即可运行对应的测试，这个是看全部代码的方式

具体通过测试看源码的案例，可以看[我的这一篇笔记](https://vixcity.gitee.io/my-gitee-blog/2021/10/19/%E3%80%90%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB%E3%80%91/)


## 准备

今天我们只看核心代码

所以我们进入example 这个 JS 文件里面去

这个案例非常的简单，一共就17行代码

![这个案例非常的简单](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222139234.png)

运行第四行作者给我们的命令

```bash
node example
```

我们发现没有反应，那么这是怎么一回事呢

我们来看一下注释

`You have to run this file two times the first time`

翻译过来就是`第一次必须运行此文件两次`

嘿，我运行过的别的程序都只需要运行一次，就你要运行两次，你搞特殊是吗😶‍

那么我们仔细来想想，要运行两次呢?

我们在来看看注释

`This is because it never reports updates on the first run`

翻译过来就是

`这是因为它不会在第一次运行时报告更新`

我们带着第一次为什么不会更新的问题来看源码

对于vscode进行node调试

我把过程放在以下gif图里面，大家可以进行参考

![调试](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222206618.gif)

## 阅读

首先我们在example里面打上断点

![断点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222209699.png)

然后进入调试

我们这时候发现，断点进来了

![断点进来了](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222210440.png)

我们点击调试的↓按钮

我们发现我们进到了index.js文件里面

所以这个就是他的核心文件

![183](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222211206.png)

我们进入到了183行

### 第一部分：实例化对象

在往下走，我们进入到了第一部分

![第一部分](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222215319.png)

我们传入的参数会被option接到

然后对拿到的对象进行数据清洗，只保留需要的数据

如果没有需要的数据，就给他抛出个错误

![错误](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222227305.png)

接下来把需要的数据挂载到实例对象身上

检查更新时间是否符合规范

> 知识点：process.env 是 Node.js 中的一个环境对象。其中保存着系统的环境的变量信息。

通过获取process检查是否禁用更新

> 知识点：isCi的作用是如果当前环境是持续集成服务器，则返回true

判断是否在Npm脚本中通知

![不禁用更新做的事情](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222234102.png)

判断一下你是否禁用更新了

>知识点：ConfigStore的作用是轻松加载和保留配置，而无需考虑在哪里以及如何

>知识点：Chalk的作用是让你的终端更美观好看，具体在[Vue-release](https://vixcity.gitee.io/my-gitee-blog/2021/10/22/%E3%80%90%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB%E3%80%91Vue-release%20%E5%B0%A4%E9%9B%A8%E6%BA%AA%E5%8F%91%E5%B8%83%20Vue.js%20%E7%9A%84%E6%B5%81%E7%A8%8B%E4%BB%A5%E5%8F%8A%E8%87%AA%E5%8A%A8%E5%8C%96%E5%8F%91%E5%B8%83%E5%8A%9F%E8%83%BD%E7%9A%84%E5%AE%9E%E7%8E%B0/)这篇文章中有提到过

如果你没禁用的话，帮你把包名通过`ConfigStore`进行持久化存储，顺便把最后一次检查的时间更新成现在

### 第二部分：检查更新

接下来我们顺着代码的路径，走到了中间这一行

![检查更新](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222300072.png)

进去的第一目光我们的卡姿兰大眼睛就可以看到好几个判断

![卡姿兰大眼睛表情包](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222306777.jpeg)

![判断](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222303556.png)

第一个判断的大概意思是

如果你没有本地存储或者禁用更新的情况下，那么就不执行了，我们就说拜拜了

接下来，他拿到我们的之前存进去的update对象来更新

#### 关键点

这里有一个关键点，就是我们之前埋下的坑

他第一次为什么不执行

因为这里第一次的时候，我们没有执行过check.js这个文件

所以我们这里的这个update是空的

这里在埋下一个点，为什么updata是空的导致他只执行一次呢，这个后文会讲到

我们接着往下看

![spawn](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222337077.png)

> 知识点：spawn => 详情请参考[spawn](https://www.npmjs.com/package/spawn)

这里通过`spawn`开启一个子进程，运行`check.js`，然后把`options`作为调用`check.js`的命令行参数

### check.js文件

接下来我们就进入到了check.js文件里面

![checkjs](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222342863.png)

```js
const options = JSON.parse(process.argv[2]);
```

这一段代码，意思是把我们刚才通过控制台传的option参数拿过来

> 知识点：latestVersion => 获取最新版本的 npm 包

拿过来之后新建一个实例	，然后去拿到对应的包名、最新版本、当前版本、版本差别的信息

拿到信息之后，更新一下最后一次检查更新对应的时间

在顺便把信息塞到持久化存储当中的update里面、

然后关闭`spawn`打开的子进程，返回原来的函数里面

### 第三部分

当拿到最新版本的信息之后，他会把对应的实例对象返回出去

返回到了example.js文件里面

返回到了example之后去执行notify

![notify](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110222356689.png)

虽然说，之前的update被存进去了，但是在notify的时候没有被读出来

所以就导致之前的update因为是空的，在最开始的时候，进不去notify的函数，就被return掉了

这就是为什么第一次执行的时候，什么都不返回的原因了

> 知识点：is-installed-globally：检查您的软件包是否已全局安装

> 知识点：is-yarn-global：检查您的yarn包是否已全局安装

接下来他会检查你是不是全局安装了，全局是不是用的yarn安装的

接下来就是控制你的控制台，在你的控制台上输入一些安装命令

> 知识点：boxen：在终端中创建框

完成之后，通过boxen构建一个框框，然后用template去显示更新信息

## 总结
1. 我们用了[[5w1h分析法]]，在看源码之前进行了分析思考
2. 我们学会了怎么去通过编译器，去调试测试用例
3. 我们知道了看源码前应该做哪些准备
4. 我们知道了可以带着问题去看源码，可以提高你的阅读效率
5. 我们知道了检查更新npm文件的全部流程
6. 我们知道了process.env 是 Node.js 中的一个环境对象，里面保存着系统的环境的变量信息
7. ConfigStore可以进行持久化存储
8. Chalk可以让你的终端更好看
9. spawn可以生成一个子进程去执行命令
10. is-installed-globally可以检查你的软件包是否已全局安装
11. is-yarn-global可以检查你全局安装的是否是通过yarn安装的
12. 在终端中画框框可以用 boxen
13. isCi的作用可以判断当前环境是持续集成服务器
14. latestVersion可以获取最新版本的 npm 包
15. 最主要的是，我们学会了怎么样去从一个啥也不是的小白，去学习，去拆分源码，解构源码，理解作者的思路与想法，我们可以把步子迈的小一点，一小步一小步的去走，步子太大了容易扯着蛋，把它掰开了揉碎了，慢慢消化吸收理解，成为自己的东西！加油，奥利给！