首先，我们进行初始化项目 
```bash
yarn init y
```
创建一个 src 文件夹

src 内放置各个模块，里面是对应的文件和文件名

先新建一个reactivity 这个是整个响应式的核心

然后在 reactivity 文件夹当中新建 index.ts 和 test

然后在 test 文件夹当中新建 index.spec.ts，这个是整个 reactivity 的单元测试

此时的文件夹如下

![文件夹](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081414064.jpg)

然后在 index.spec.ts 里面写上如下代码

```js
it('init', () => {
 	expect(true).toBe(true)
});
```

会发现他报错了

![报错](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081416732.jpg)

那么这个问题的原因是什么呢

不买关子，这个问题的核心就是我们没有集成 ts

我们执行一下代码，集成一下 ts

```bash 
npm i typescript -g -S 
或者
yarn add typescript
```
 个人比较推荐 yarn ,他的速度非常快
 
 没有 yarn 的，可以t通过以下命令安装一下 yarn 
 ```bash
 npm install -g yarn
 ```

等安装完成之后执行以下命令
```bash
npx tsc --init 
```
执行完成之后，会出现一个 tsconfig.json 文件（下面的 yarn.lock 文件是刚刚我们 yarn add 创建）

![tsconfig.json](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081429754.jpg)

现在我们发现还是报错

我们还需要引入一个依赖，具体如下

```bash
yarn add jest @types/jest --dev
```

安装完成之后，我们需要在 tsconfig.json 文件当中找到 types 命令，在里面加上 jest

```json 
"types": ["jest"],
```
加完之后我们回来在看

发现报错就没了

![没报错](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081453105.jpg)

然后我们去 package.json 里面添加 script 命令

```json 
"scripts": {
	 //	添加 test 这条命令，让他可以在 test 的时候执行 jest
	 "test": "jest",
 },
```

接下来，我们去研究一下模块的导出会不会报错

在 index.ts 文件中写上

```ts
export function add (a,b) {
 	return a+b
};
```
此时我们会发现，我们的代码，<font color=#FF0000 size=4 face="黑体">飘红了</font>
![飘红了](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081523994.jpg)

这个是 ts 当中类型的问题，我们在去 tsconfig.json 当中找到 "noImplicitAny": true, 取消掉注释

然后改成 false 就可以了

![tsconfig.json](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081530646.jpg)

更改完成之后，我们就发现他不报错了

![改完之后](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081531832.jpg)

接下来我们进行测试

在 index.spec.ts 中引入刚刚写好的 add 模块

执行测试，发现他报错了

![又一次报错](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081539332.jpg)

原因是 jest 测试是在 node 环境的，他默认用的是 commonJS 规范，而我们用的是 ESM 规范

那么我们需要用 babel 去给他转换一下

用以下命令添加 babel 依赖

```bash
yarn add --dev babel-jest @babel/core @babel/preset-env
```

然后在根目录添加 babel.config.js，添加如下代码

```js 
module.exports = {
	presets: [["@babel/preset-env", {targets: {node: "current"}}]],
}
```

接下来我们需要让 babel 支持 ts

添加依赖

```bash
yarn add --dev @babel/preset-typescript
```

在 babel.config.js 添加 "@babel/preset-typescript" ，现在 babel.config.js 中的代码如下
```js 
module.exports = {
	 presets: [
		 ["@babel/preset-env", { targets: { node: "current" } }],
		 "@babel/preset-typescript",
	 ],
};
```

到目前为止我们添加测试和集成 ts 环境就成功了

![测试成功](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081600938.png)

这个测试通过之后我们的单元测试就配置的差不多了，接下来就可以开心的敲代码了