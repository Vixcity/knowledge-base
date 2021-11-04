## 缘由

Vue3中尤大说了移除中央事件总线这一消息

[详细地址请戳这里](https://v3.cn.vuejs.org/guide/migration/events-api.html#%E6%A6%82%E8%A7%88)

那么既然这样子说了

尤大肯定为我们这些可爱开发者用户们考虑好了替换方案

![方案](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202111040926505.jpg)

他说事件总线模式可以被替换为使用外部的、实现了事件触发器接口的库，例如 [mitt](https://github.com/developit/mitt) 或 [tiny-emitter](https://github.com/scottcorgan/tiny-emitter)

那么我们今天就好好的来看看这两个库的源码

看看他们到底是怎么实现的吧

## [mitt](https://github.com/developit/mitt)

首先我们来看mitt这个库的自我介绍吧

![readme](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202111040930142.jpg)

说只有200字节

这么短小精悍的代码我们总是得来看看的嘞

我们先克隆一下他的[仓库](https://github.com/developit/mitt)

然后通过 yarn 或者 npm 安装依赖

这里就不过多赘述

## 问题

这时我们进到测试

发现他报错了

![报错](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202111041053193.jpg)

怎么回事呢？

我们明明都没碰过它

怎么就报错了呢

这个是因为`..`他会自动去找外面的index文件，我们现在外面没有index文件，所以他就报错了

那么应该怎么解决呢

## 解决

有以下两个解决办法

### 方法1：

把引入的地方改一下

改成如下图所示

![改动](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202111041058134.jpg)

不过这个方法不太推荐

因为我们是去读，去欣赏源码的

而不是去改动源码的

就像我们去看，去欣赏风景的

而不是在风景上刻画上到此一游的

所以就有了第二个解决办法

### 方法2：

既然作者这么写了

那么肯定是有他的道理的

我们想想应该怎么样才能在外面有一个 index 文件呢？

这时候有些聪明的小脑袋瓜就想到了

那就是运行一下打包程序

打包了之后，肯定就有新生成的文件了

那么我们来打包试一下

进到 vscode 里面

打开 package.json 文件

找到打包命令 build

![build](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202111041104918.jpg)

点击调试，运行 build 命令

![build](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202111041106020.jpg)