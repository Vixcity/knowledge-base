## 你应该知道的 10 个 VS Code 特性和技巧

### 1.带连字符的字体

连字的字体能让你的代码看起来更干净、更方便

我们可以通过结合连字的特殊字体使您的编辑器体验更好

有好几种支持连字的字体可用，但我还是推荐经典的 **[Fira Code](https://github.com/tonsky/FiraCode/releases/download/5.2/Fira_Code_v5.2.zip)**

经典永不落时~

![Fira Code](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109112341621.webp)

要在 VS Code 中启用连字，只需在 VS Code 设置文件中添加

**“editorLigatures”：true** 即可

![设置文件](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109112352706.jpg)

### 2.彩虹缩进

如果你使用 **JavaScript** 和 **Python** 编写代码

那么 VS Code 扩展会为文本前面的缩进着色

总共会交替使用四种特殊颜色

此功能对于外观整洁的代码非常有用

插件名称：

>indent-rainbow
### 3.标签包装
如果你厌倦了重复打字那么你应该了解`emmet`

`emmet`允许你编写一个缩写的代码并快速获取返回的相应标签

最新版本的 `emmet` 支持大多数 `Emmet` 操作，包括扩展 `Emmet` 缩写和片段

要使用标签包装：

- 选择文本
- 打开命令面板（ctrl + shift + p）
- 执行**Emmet: Wrap with Abbreviation**
- 输入标签 **div**（或缩写）
- 然后按 **Enter**

### 4.Turbo Console.log()

![Turbo](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109120004187.webp)

当我们只需要快速输出某些内容时，我们便应该节省时间，而不是重复键入 **console.log**

**Turbo Console Log** 是 VS Code 中一个很棒的扩展

它解决了这个问题，它可以在下面的行中记录任何变量，并在代码结构之后自动添加前缀

### 5.多光标编辑

![多光标编辑](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109120005064.webp)

我个人喜欢 VS Code 中最有用的功能之一，通过多光标编辑，您可以快速编辑多行。多光标编辑有以下几种方法：

- **ALT-CLICK**：如果你想在一个文件中插入多个不在连续行上的光标，你只需简单地按住ALT并点击每一行
- **CTRL-U**：万一你点击了错误的行，又不想重新选择行，那么只要按CTRL+U就可以撤销上次的光标操作
- **SHIFT-ALT**：用方向键。如果你选择了一个词，并希望包括引号或它附近的任何东西，只需简单地使用SHIFT+ALT与方向键来扩大你的选择区域
- **SHIFT-ALT-Drag**：使用此组合键可以创建矩形选区
### 6.Polacode

![Polacode](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109120007241.webp)

如果你熟悉分享源代码截图的著名工具（**Carbon.sh**）

但所有这些工具都需要付出更多努力来构建吸引人的源代码截图

**Polacode** 以非常简单的方式完成相同的工作

我们可以使用 Carbon 中无法使用的默认编辑器字体

插件名称：
> _polacode_-2020

### 7.排序
当你想对数据或变量列表进行排序时

我们只需选择要排序的行并使用命令面板 (**F1**)

然后搜索排序就可以选择升序或降序排序

### 8.注释

添加注释是开发人员在团队中工作时经常做的事情

在 VS Code 中，您可以眨眼注释掉单行或多行

尽管有两种方法可以添加注释

- 您可以通过按 **Ctrl** 或 **Cmd + /** 对一行进行注释，再次按下它将取消对该行的注释
- 要创建注释块，请选择您想要注释的选项并按下 **Shift+Alt+ A**

### 9.自定义侧边栏

在编写代码时，我们总是需要尽可能多的空间

这时候我们可以通过按 **Ctrl + B** 关闭侧边栏，也可以再次按 **Ctrl + B** 显示侧边栏

此外，如果你想打开它到一个特定的菜单

那么你可以使用下面给出的特定菜单的键盘快捷方式

![键盘快捷方式](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109120012964.webp)
### 10.自定义片段

没有人喜欢一遍又一遍地输入相同的代码，片段扩展为我们做了这项工作

但有些时候，我们只是需要一个自定义的代码段来使我们的工作更容易

在VS Code中，你可以创建自定义代码片段，减少输入相同代码的重复

要创建自定义代码段：
- 转到设置图标并选择**User Snippets**，现在选择您想要创建的片段的语言。
- 例如，如果你想为 JavaScript 项目创建自定义代码段。你必须编写key/value对，其中key包含定义的数据。**prefix**（前缀）定义了代码段的触发词，**body**（正文）是将要插入的代码，**description**（描述）是可选的，由 IntelliSense 显示。

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109120014898.webp)