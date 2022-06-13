今天我又来看源码了，今天来看 vue-devtools 的源码

我们来看看 github 上 vue-devtools 的源码

我们看到它的 readme 里面有个 [Documentation](https://devtools.vuejs.org/) 

这个点进去就是他的官网（PS：现在 vue-devtools 也有官网了）

首先点击右上角的 Gride 或者点击中间的 Get Star 按钮

- [ ] ![](https://s2.loli.net/2022/06/10/7EZq3Qau8IYMVxL.png)

点击我们会来到这个界面

![主界面](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132218919.png)

这个是安装的界面，我们按照界面提示安装一下

因为我们是要研究这个源码的，所以我们继续往下看

点击到这个 Open component in editor（翻译：在编辑器中打开组件）

![Open component in editor](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132221479.png)

点开我们可以看到主页面有这么一段话

> Vue CLI 3 supports this feature out-of-the-box when running `vue-cli-service serve`.
> 翻译：在运行“vue-cli-service serve”时，Vue CLI 3支持此功能。

啥意思呢，说人话就是

我们可以在 vue-cli-service serve 的时候，在编辑器中打开组件

![点击组件](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132227239.jpg)

当你运行 vue-cli-service serve 的时候，直接就可以开箱即用这个功能

那他是怎么做到的呢？这个就是我们今天要阅读的源码的内容了~

首先我们拿一下川神大佬的 git 仓库地址，来开始今天的源码阅读

```bash
git clone https://github.com/lxchuan12/open-in-editor.git
```

github 访问速度慢的话可以去[羽翼城大佬的博客](https://www.dogfight360.com/blog/475/)

下载软件然后去进行加速

当我们克隆完成之后，我们进入到 vue3-project 去安装依赖

```bash
npm install 
# OR 
yarn
```

我们看到官网上有这么一句话

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132248288.png)

我们可以猜测

开箱即用应该和下面这个模块有关系

`launch-editor-middleware`

我们去找找看这个模块

![没找到](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132251755.png)

我们发现找到的都是在 yarn 里面的，明显和我们的需求不符

众所周知，yarn.lock 是 yarn 安装依赖包的记录

所以我们找到的这个是不对的

我们就可以猜测，这个包是在 node_modules 中的，所以 vscode 会自动忽略

那怎么办呢

我们可以点击...

![...](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132255684.png)

我们就可以发现有一个排除的文件的选项，我们点击它的齿轮的图标

![排除的文件](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132256997.png)

就会有一大堆的东西出来了，其实点击这个图标是让他去之前忽略的地方去找

此时我们可以发现在这么一大堆的东西里面，有两个可以的目标

![可疑](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132258697.png)

现在有两个可疑人物该怎么办呢？

不着急，我们在去官网上看一下

有一个这么个代码段，就在刚才的 `launch-editor-middleware` 下面

```js
app.use('/__open-in-editor', openInEditor())
```

![官网](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132301516.png)

那我们就可以在这两个文件里面去进行寻找，看看那个文件有这个代码段的

那他就是符合我们的期望的文件

![serve](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132304151.png)

我们可以在 serve.js 里面找到它，那么我们就在这个地方打上断点

我们启动一下项目

![项目启动成功](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132341295.png)

项目启动成功

如果你发现你的浏览器当中，vue 的图标没有亮起来，像以下这样的话，请检查一下你的 vue-devtools 的版本

![不亮与亮](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132344769.png)
  
 上面左边这个 vue-devtools 是对应的下面左边的那个插件，上面右边对应下面右边的插件
 
 ![插件](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132346473.png)
 
 正式版的插件是不会亮的，需要 beta 版本的插件才会亮起来，这是一个小小的坑
 
 vue3 项目需要右边的插件才会亮起来
 
 我们点击一下以下地方，他会跳到编辑器
 
 ![开始](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132351732.png)
 
 我们发现，点这里刚刚那个断点没进来
 
 那么我们在进去点击这个函数进到这个函数里面来打断点
 
 ![点进去](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132352202.png)
 
 点进去这个标红的函数
 
 进到这个文件，在该行打上断点
 
 ![断点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132354565.png)
 
 再次点击浏览器的打开文件按钮
 
 发现成功进到断点里面来了
 
 ![进来了](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109132355684.png)
 
 接下来我们就可以愉快的调试了~
 
开始调试！

首先我们进来上图的位置

这个是要在`launch-editor-middleware`中间件中

最终是调用 `launch-editor` 打开文件

```js
launch(path.resolve(srcRoot, file), specifiedEditor, onErrorCallback)
```

因为 launch 是在 launch-editor 文件里面的

我们点击函数名跳转进来该文件，并且打上断点

![文件](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109141047648.jpg)

我们慢慢的一步一步调试

```js
if (!fs.existsSync(fileName)) {
	// 判断文件是否存在，不存在，直接返回。
 	return
}

if (typeof specifiedEditor === 'function') {
	// 如果 specifiedEditor => 这里传递过来的则是函数 
	// 那么和 onErrorCallback 切换下，把它赋值给错误回调函数
	 onErrorCallback = specifiedEditor
	 specifiedEditor = undefined
}

// 包裹一层函数
onErrorCallback = wrapErrorCallback(onErrorCallback)
// 猜测当前进程运行的是哪个编辑器
const [editor, ...args] = guessEditor(specifiedEditor)
if (!editor) {
	 onErrorCallback(fileName, null)
	 return
}
```

↓这段的代码，就是传递错误回调函数

`wrapErrorCallback` 返回给一个新的函数

`wrapErrorCallback` 执行时，再去执行 onErrorCallback(cb)

```js
onErrorCallback = wrapErrorCallback(onErrorCallback)
```

guessEditor 猜测当前正在使用的编辑器

我们再进去看看他具体是干啥的

先是来了一个判断

```js
// 如果具体指明了编辑器，则解析下返回。
// 如果指定了编辑器，则解析一下，这里没有传入。如果自己指定了路径。 
// 比如 c/Users/lxchu/AppData/Local/Programs/Microsoft VS Code/bin/code 
// 会根据空格切割成 c/Users/lxchu/AppData/Local/Programs/Microsoft

if (specifiedEditor) {
	return shellQuote.parse(specifiedEditor)
}
```
 
 然后在去调用 node 的原生 api 去看看调用他的平台是哪一个
 
 ```js
// 找出当前进程中哪一个编辑器正在运行。`macOS` 和 `Linux` 用 `ps x` 命令 `windows` 则用 `Get-Process` 命令

try {
	//  代码有删减
	if (process.platform === 'darwin') {
	  const output = childProcess.execSync('ps x').toString()
	  // 省略
	} else if (process.platform === 'win32') {
	  const output = childProcess
		.execSync('powershell -Command "Get-Process | Select-Object Path"', {
		  stdio: ['pipe', 'pipe', 'ignore']
		})
		.toString()
		// 省略
	} else if (process.platform === 'linux') {
	  const output = childProcess
		.execSync('ps x --no-heading -o comm --sort=comm')
		.toString()
	}
} catch (error) {
	// Ignore...
}
 ```
接着我们来看他的剩余部分

```js
const childProcess = require('child_process')

if (process.platform === 'win32') {
    // On Windows, launch the editor in a shell because spawn can only
    // launch .exe files.
    _childProcess = childProcess.spawn(
        'cmd.exe',
        ['/C', editor].concat(args),
        { stdio: 'inherit' }
    )
    } else {
    _childProcess = childProcess.spawn(editor, args, { stdio: 'inherit' })
}
```

这段代码讲的是

用子进程模块

简单来说子进程模块有着执行命令的能力

原理其实就是利用`nodejs`中的`child_process`

执行了类似`code path/to/file`命令

OK，本篇完~