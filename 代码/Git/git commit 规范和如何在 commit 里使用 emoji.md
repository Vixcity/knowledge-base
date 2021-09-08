# 【推荐】git commit 规范和如何在 commit 里使用 emoji

## 前言

git commit message 可以大体上分成两种类型去看待。

-   一是多人维护的，别人很容易来看 commit message ，对于通用库、开源项目这类多人维护，而且极大可能别人会来看这份代码的，一定要规范，特别是对于开源项目，往往会要列 Change Log，规范好 commit 节省自己整理 log 的时间，也方便了用户。    
-   二是单人负责的，一般别人也不看 commit message 的，对于业务代码之类的，其实不搞问题也不大，因为极小可能会有人来看你的 commit message。但是这里我依然还是建议规范一下，不然一旦后面想去看就会花费很多的力气，毕竟规范一下也不麻烦。
    
主要有以下三部分内容：
```bash
	1.  了解 git commit 规范
	2.  学到用工具 Commitizen 来规范 commit
	3.  学到如何在 commit message 里加上好看的 emoji    
```
## git commit message 规范

现在主流的 commit message 规范是 [Angular 团队所用的准则](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines)，继而衍生了 [Conventional Commits specification](https://link.zhihu.com/?target=https%3A//conventionalcommits.org/)， 很多工具也是基于此规范。

每次提交，commit message 都包括三个部分：header，body 和 footer，其中 header 有一个特殊的格式，包括了 type、scope、subject。

```bash
<type>(<scope>): <subject>  
<BLANK LINE>  
<body>  
<BLANK LINE>  
<footer>  
```

其中 header 是必选的，但是 header 里的 scope 是可选的，另外提交的 message 长度不要超过 100 个字符，太长了不易阅读。

对于我个人来说，我觉得只要把 header 部分规范其实基本能满足绝大部分需要了，强制去规范 body 和 footer 对于团队的同学来说简直是折磨，所以我这里仅介绍 header 的几部分

### type

type 指明 git commit 的类别，应该使用以下类型

-   『feat』: 新增功能
    
-   『fix』: 修复 bug
    
-   『docs』: 仅仅修改了文档，比如 README, CHANGELOG 等等
    
-   『test』: 增加/修改测试用例，包括单元测试、集成测试等
    
-   『style』: 修改了空行、缩进格式、引用包排序等等（不改变代码逻辑）
    
-   『perf』: 优化相关内容，比如提升性能、体验、算法等
    
-   『refactor』: 代码重构，「没有新功能或者 bug 修复」
    
-   『chore』: 改变构建流程、或者增加依赖库、工具等
    
-   『revert』: 回滚到上一个版本
    
-   『merge』: 代码合并

### scope（可选）

scope 用于说明 commit 影响的范围，根据不同项目有不同层次描述。若没有特殊规定，也可以描述影响的哪些功能等。

### subject

subject 是 commit 目的的简短描述，不超过 50/80 个字符，一般 git 提交的时候会有颜色提示。

-   若英文用不惯，那么推荐使用中文
    
-   若是开源代码，一律推荐统一英文，英文不行可以翻译软件用起来
    
-   若是开源代码，可以再附加对应的 issue 地址
    
-   结尾不加标点符号
    

## 工具：Commitizen

Commitizen 是一个撰写合格 commit message 的工具,（详细说明见 [github 地址](https://github.com/commitizen/cz-cli)），用于代替 git commit 指令，而 cz-conventional-changelog 适配器提供 conventional-changelog 标准（约定式提交标准）。基于不同需求，也可以使用不同适配器。

1. 全局安装 commitizen cz-conventional-changelog

```bash
npm install -g commitizen cz-conventional-changelog  
```

2. git cz 取代 git commit

安装完毕后，可直接使用 git cz 来取代 git commit。

![git cz](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109080953517.jpg)

可以看到当你 cz 之后，下面有很多的 type 让你选择，这就省掉了每次你去想应该用哪种 type，非常方便。

> 全局模式下，需要 `~/.czrc` 配置文件, 为 commitizen 指定 Adapter, 如果你不需要生成 Change Log 可以不用去处理。

只要用了 Commitizen ，就很方便的就规范的大家的 message 。

## 在 commit message 里用 emoji

在 git commit 上使用 emoji 提供了一种简单的方法，仅通过查看所使用的表情符号来确定提交的目的或意图, 提交记录非常好理解，阅读体验非常棒。

![表情符号来](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109080954608.jpg)

### 使用方法

git commit 时直接在注释中写入表情符号的代码部分即可完成表情 emoji 的提交。

举个栗子：

```bash
git commit -m ':tada: init commit'  
git commit -m 'added image to repo :art:'  
```

> 如果你用 Commitizen，可能不太好把 emoji 放在最前面，因为咱们操作的空间，就在 git cz 后第三次交互提示，也就是在输入 header 的 subject 的时候填入，如果是这样，可以选择把表情放到最后。

效果展示：![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109080956473.jpg)

如果你们团队是一个喜欢用 emoji 的团队，其实可以不用遵循上面的一节的规范，因为 emoji 已经可以表达出 header 的 type 的作用了，直接在 emoji 后面加描述即可。

### emoji 表情库
| emoji | emoji 代码 | commit 说明| 
| :----: | :----: | :----: |
| :tada: (庆祝) | :tada: | 初次提交 |
| :new: (全新) | :new: | 引入新功能 |
| :bookmark: (书签) | :bookmark: | 发行/版本标签|
| :bug: (bug)| :bug: | 修复 bug |
| :ambulance: (急救车) | :ambulance: |重要补丁|
| :globe_with_meridians: (地球) | :globe_with_meridians: | 国际化与本地化|
| :lipstick: (口红) | :lipstick: | 更新 UI 和样式文件 |
| :clapper: (场记板) | :clapper: | 更新演示/示例 |
| :rotating_light: (警车灯) | :rotating_light: | 移除 linter 警告 | 
| :wrench: (扳手) | :wrench: | 修改配置文件 |
| :heavy_plus_sign: (加号) | :heavy_plus_sign: | 增加一个依赖 |
| :heavy_minus_sign: (减号) | :heavy_minus_sign: | 减少一个依赖 |
| :arrow_up: (上升箭头) | :arrow_up: | 升级依赖 | 
| :arrow_down: (下降箭头) | :arrow_down: | 降级依赖 |
| :zap: (闪电)  | :zap: | 提升性能 |
| :racehorse: (赛马) | :racehorse: | 提升性能 |
| :chart_with_upwards_trend: (上升趋势图) | :chart_with_upwards_trend: | 添加分析或跟踪代码
| :rocket: (火箭) | :rocket: | 部署功能 | 
| :white_check_mark: (白色复选框) | :white_check_mark: | 增加测试 | 
| :memo: (备忘录)  | :memo: | 撰写文档 |
| :book: (书) | :book: | 撰写文档 | 
| :hammer: (锤子) | :hammer: | 重大重构 | 
| :art: (调色板) | :art: | 改进代码结构/代码格式 |
| :fire: (火焰) | :fire: | 移除代码或文件 |
| :pencil2: (铅笔) | :pencil2: | 修复 typo |
| :construction: (施工) | :construction: | 工作进行中 |
| :wastebasket: (垃圾桶) | :wastebasket: | 废弃或删除 | 
| :wheelchair: (轮椅) | :wheelchair: | 可访问性 |
| :construction_worker: (工人) | :construction_worker: | 添加 CI 构建系统 |
| :green_heart: (绿心) | :green_heart: | 修复 CI 构建问题 |
| :lock: (锁) | :lock: | 修复安全问题 |
| :whale: (鲸鱼) | :whale: | Docker 相关工作 |
| :apple: (苹果) | :apple: | 修复 macOS 下的问题 |
| :penguin: (企鹅) | :penguin: | 修复 Linux 下的问题 | 
| :checkered_flag: (旗帜) | :checkered_flag: | 修复 Windows 下的问题 |
| :twisted_rightwards_arrows: (交叉箭头) | :twisted_rightwards_arrows: |分支合并 |

### 如何在命令行中显示 emoji

默认情况下，在命令行中并不会显示出 emoji, 仅显示 emoji 代码。不过可以使用 emojify 使得在命令行也可显示 emoji, 它是一个 shell 脚本，安装与使用都很简单，[在这里](https://github.com/mrowa44/emojify) 查看更详细的如何安装与使用。

#### 安装 emojify

```bash
sudo sh -c "curl https://raw.githubusercontent.com/mrowa44/emojify/master/emojify -o /usr/local/bin/emojify && chmod +x /usr/local/bin/emojify"  
```

#### 使用 emojify

```bash
emojify "Hey, I just :raising_hand: you, and this is :scream: , but here's my :calling: , so :telephone_receiver: me, maybe?"  
```

显示效果：
![显示效果](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081014266.jpg)

在使用 git bash 的时候，设置替换日志中的表情符号代码

```bash
git log --oneline --color | emojify | less -r  
```

或者设置 git log 命令别名

```bash
alias gitlog='git log --oneline --color | emojify | less -r'    
gitlog  
```

效果如下：
![效果如下](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081016202.jpg)

是不是很好玩，快去试试吧！

## 参考资料
-  [ git-emoji-guide](https://hooj0.github.io/git-emoji-guide/)
-  [ git commit 时使用 Emoji ?](https://zhuanlan.zhihu.com/p/29764863)