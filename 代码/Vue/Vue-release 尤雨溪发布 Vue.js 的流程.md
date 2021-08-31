 首先了解下`vuejs`的发布流程
 
![vuejs的发布流程](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311424559.png)

# 环境准备
你需要确保 Node.js 版本是 `10+`, 而且 `yarn` 的版本是 `1.x` Yarn 1.x。

如果你安装的 `Node.js` 版本是低于 `10`的话，推荐用 nvm 这个`Node版本管理工具`

## vue-next ==> package.json
```json 
// vue-next/package.json
{
    "private": true,
    "version": "3.2.4",
    "workspaces": [
        "packages/*"
    ],
    "scripts": {
        // --dry 参数是自己加的，如果是调试 代码也建议加
        // 这句话的意思是
		// 不执行测试和编译 、不执行 推送git等操作
        // 也就是说空跑，只是打印
        "release": "node scripts/release.js --dry",
        "preinstall": "node ./scripts/checkYarn.js",
    }
}

```
## 调试 vue-next ==> scripts ==> release.js 文件
打开 VScode ，其版本最好是大于`1.50.0`

找到 `vue-next ==> package.json` 文件打开

然后在 `scripts` 上方，会有调试按钮

![点击](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311441685.jpg)

点击后，选择 `release`。即可进入调试模式

![点击release](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311442086.jpg)

此时我点击了之后，发生了这样一件恐怖的事情

![调试失败了](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311454692.png)

<font color=#FF0000 size=7 face="黑体">竟然调试失败了！！！</font>

那是什么原因造成的呢

既然是调试过程中出的问题，那我们打开 VScode 找到调试去里面看看

![运行与调试](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311459643.jpg)

这时候我们发现，他说我们要自定义调试，要打开文件夹，在一看，我们发现，我现在打开的是一个文件，所以它找不到文件夹当中的东西，于是现在就报错了

那我们现在打通过VScode打开文件夹重新来一遍

这时候我们点击调试，点击一下 release，发现

![好使](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311503677.jpg)

诶~

一下子好使了

学会调试后，我们先大致走一遍流程，在关键地方多打上几个断点多走几遍，就能猜测到源码意图了。

<font color=#00FFFF size=7 face="黑体">本篇文章到此结束，完结撒花~~</font>

别走别走，哈哈，我开玩笑的

刚刚那个调试的问题解决，接下来我们就开始愉快的调试环节🤪

# 1. 文件开头的一些依赖引入和函数声明
## 1.1 第一部分
让我们跟着断点来，先看文件开头的一些依赖引入和函数声明
```js
// vue-next/scripts/release.js
const args = require('minimist')(process.argv.slice(2))
// 文件模块
const fs = require('fs')
// 路径
const path = require('path')
// 控制台
const chalk = require('chalk')
const semver = require('semver')
const currentVersion = require('../package.json').version
const { prompt } = require('enquirer')

// 执行子进程命令   简单说 就是在终端命令行执行 命令
const execa = require('execa')

```
### 1.1.1 minimist 命令行参数解析
```js
// 第一句话
const args = require('minimist')(process.argv.slice(2))
//其中`process.argv`的第一元素是`Node`可执行文件和第二个元素是被执行JavaScript文件的完全限定的文件系统路径，无论你是否这样输入他们，他们都会被获取。
```

其中，这个 minimist 这个库，就是解析命令行参数的

我们来看下川神的例子
```js
$ node example/parse.js -a beep -b boop
// 这个被minimist解析出来是这样子的
{ _: [], a: 'beep', b: 'boop' }

$ node example/parse.js -x 3 -y 4 -n5 -abc --beep=boop foo bar baz
// 这个被minimist解析出来是这样子的
// '_' 这个属性所对应的数组值，是包含所有没有关联选项的参数
{ _: [ 'foo', 'bar', 'baz' ],
  x: 3,
  y: 4,
  n: 5,
  a: true,
  b: true,
  c: true,
  beep: 'boop' }

```
### 1.1.2 chalk 终端多色彩输出
[chalk](https://github.com/chalk/chalk)

简单说，这个是用于终端显示<font color=#FF0000>花</font><font color=#FFFF00>里</font><font color=#FFA500>胡</font><font color=#00FF00>哨</font>多色彩输出。

![终端输出](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311525941.jpg)

### 1.1.3 semver 语义化版本

[semver](https://github.com/npm/node-semver)

语义化版本的nodejs实现，就是可以用人眼看出来的版本，用于版本校验比较等。关于语义化版本可以看这个[语义化版本 2.0.0 文档](https://semver.org/lang/zh-CN/)

> 版本格式：主版本号.次版本号.修订号，版本号递增规则如下：  
> 主版本号：当你做了不兼容的 API 修改，  
> 次版本号：当你做了向下兼容的功能性新增，  
> 修订号：当你做了向下兼容的问题修正。  
> 先行版本号及版本编译信息可以加到“主版本号.次版本号.修订号”的后面，作为延伸。
### 1.1.4 enquirer 交互式询问 CLI

简单说就是交互式询问用户输入，你敲在终端上面的东西会被他接收。

[enquirer](https://github.com/enquirer/enquirer)

### 1.1.5 execa 执行命令

简单说就是执行命令的，帮你自动化输入命令,类似我们自己在终端输入命令，比如 `echo Vixcity`。

[execa](https://github.com/sindresorhus/execa)

```js
// 例子
const execa = require('execa');

(async () => {
  const {stdout} = await execa('echo', ['Vixcity']);
  console.log(stdout);
  //=> 'Vixcity'
})();
```

到此，第一部分的引入的依赖包的内容我们就看完了，接着我们来看第二部分的内容（10-18行）

## 1.2 第二部分
```js 
// vue-next/scripts/release.js

// 对应 yarn run release --preid=beta
// beta
const preId =
  args.preid ||
  (semver.prerelease(currentVersion) && semver.prerelease(currentVersion)[0])
// 对应 yarn run release --dry
// true
const isDryRun = args.dry
// 对应 yarn run release --skipTests
// true 跳过测试
const skipTests = args.skipTests
// 对应 yarn run release --skipBuild 
// true
const skipBuild = args.skipBuild

// 读取 packages 文件夹，过滤掉 不是 .ts文件 结尾 并且不是 . 开头的文件夹
const packages = fs
  .readdirSync(path.resolve(__dirname, '../packages'))
  .filter(p => !p.endsWith('.ts') && !p.startsWith('.'))
```

这一部分川神并没有具体详细的进行解释，那么这里我就来补充一下

```js
const preId =
  args.preid ||
  (semver.prerelease(currentVersion) && semver.prerelease(currentVersion)[0])
```

10-12 ：这里的 args 是之前获取的命令行里面的参数，里面的 preid 应该是预发布的版本号

如果没有预发布的版本号的话，那么就看当前的版本号以及当前版本号的第0个元素是否存在，进行返回一个布尔值

13 ：判断是否为 dry 启动的，如果是的话，那么就跳过一些发布版本之类的重要环节

14-15 ：是否跳过测试和构建的环节

16-18 ：读取 packages 文件夹，过滤掉 不是 .ts文件 结尾 并且不是 . 开头的文件夹
## 1.3 第三部分
```js
// vue-next/scripts/release.js

// 跳过的包
const skippedPackages = []

// 版本递增
const versionIncrements = [
  'patch',
  'minor',
  'major',
  ...(preId ? ['prepatch', 'preminor', 'premajor', 'prerelease'] : [])
]

const inc = i => semver.inc(currentVersion, i, preId)
```

这一块可能不是很好理解。`inc`是生成一个版本。更多可以查看[semver文档](https://github.com/npm/node-semver#prerelease-identifiers)

```js
semver.inc('3.2.4', 'prerelease', 'beta')
// 3.2.5-beta.0
```

## 1.4 第四部分
第四部分声明了一些执行脚本函数等

```js
// vue-next/scripts/release.js

// 获取 bin 命令
const bin = name => path.resolve(__dirname, '../node_modules/.bin/' + name)
const run = (bin, args, opts = {}) =>
  execa(bin, args, { stdio: 'inherit', ...opts })
const dryRun = (bin, args, opts = {}) =>
  console.log(chalk.blue(`[dryrun] ${bin} ${args.join(' ')}`), opts)
const runIfNotDry = isDryRun ? dryRun : run

// 获取包的路径
const getPkgRoot = pkg => path.resolve(__dirname, '../packages/' + pkg)

// 控制台输出
const step = msg => console.log(chalk.cyan(msg))
```

### 1.4.1 bin 函数

获取 `node_modules/.bin/` 目录下的命令，整个文件就用了一次。

其中

```js
const dryRun = (bin, args, opts = {}) =>
  console.log(chalk.blue(`[dryrun] ${bin} ${args.join(' ')}`), opts)
```

以上这段代码对应我们刚才的启动起来的方式，是 dry 方式启动起来的话，那么就不做啥具体的内容，只是打印

```js
bin('jest')
```

相当于在命令终端，项目根目录 运行 `./node_modules/.bin/jest` 命令。

### 1.4.2 run、dryRun、runIfNotDry

```js
const run = (bin, args, opts = {}) =>
  execa(bin, args, { stdio: 'inherit', ...opts })
const dryRun = (bin, args, opts = {}) =>
  console.log(chalk.blue(`[dryrun] ${bin} ${args.join(' ')}`), opts)
const runIfNotDry = isDryRun ? dryRun : run
```

`run` 真实在终端跑命令，比如 `yarn build --release`

`dryRun` 则是不跑，只是 `console.log();` 打印 'yarn build --release'

`runIfNotDry` 如果不是空跑就执行命令。isDryRun 参数是通过控制台输入的。`yarn run release --dry`这样就是`true`。`runIfNotDry`就是只是打印，不执行命令。这样设计的好处在于，可以有时不想直接提交，要先看看执行命令的结果。不得不说，尤大就是会玩。

在 `main` 函数末尾，也可以看到类似的提示。可以用`git diff`先看看文件修改。

```js
if (isDryRun) {
  console.log(`\nDry run finished - run git diff to see package changes.`)
}
```

看完了文件开头的一些依赖引入和函数声明等，我们接着来看`main`主入口函数。

# 2. main 主流程
第2节，主要都是`main` 函数拆解分析

## 2.1 流程梳理 main 函数
```js
const chalk = require('chalk')
const step = msg => console.log(chalk.cyan(msg))
// 前面一堆依赖引入和函数定义等
async function main(){
  // 版本校验

  // run tests before release
  step('\nRunning tests...')
  // update all package versions and inter-dependencies
  step('\nUpdating cross dependencies...')
  // build all packages with types
  step('\nBuilding all packages...')

  // generate changelog
  step('\nCommitting changes...')

  // publish packages
  step('\nPublishing packages...')

  // push to GitHub
  step('\nPushing to GitHub...')
}

main().catch(err => {
  console.error(err)
})
```

上面的`main`函数省略了很多具体函数实现。接下来我们拆解 `main` 函数。

## 2.2 确认要发布的版本

第一段代码虽然比较长，但是还好理解。 主要就是确认要发布的版本。

调试时，我们看下这段的两张截图，就好理解啦。

![终端输出选择版本号](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311622321.jpg)

![终端输入确认版本号](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202108311622717.jpg)

```js
// 根据上文 mini 这句代码意思是 yarn run release 3.2.4 
// 取到参数 3.2.4
// 我们是dry方式启动的，所以现在这个targetVersion为undefined
let targetVersion = args._[0]

if (!targetVersion) {
  // no explicit version, offer suggestions
  const { release } = await prompt({
    type: 'select',
    name: 'release',
    message: 'Select release type',
    choices: versionIncrements.map(i => `${i} (${inc(i)})`).concat(['custom'])
  })

// 选自定义
  if (release === 'custom') {
    targetVersion = (
      await prompt({
        type: 'input',
        name: 'version',
        message: 'Input custom version',
        initial: currentVersion
      })
    ).version
  } else {
    // 取到括号里的版本号
    targetVersion = release.match(/\((.*)\)/)[1]
  }
}

// 校验 版本是否符合 规范
if (!semver.valid(targetVersion)) {
  throw new Error(`invalid target version: ${targetVersion}`)
}

// 确认要 release
const { yes } = await prompt({
  type: 'confirm',
  name: 'yes',
  message: `Releasing v${targetVersion}. Confirm?`
})

// false 直接返回
if (!yes) {
  return
}
```
## 2.3 执行测试用例
```js
// run tests before release
step('\nRunning tests...')
if (!skipTests && !isDryRun) {
  await run(bin('jest'), ['--clearCache'])
  await run('yarn', ['test', '--bail'])
} else {
  console.log(`(skipped)`)
}
```