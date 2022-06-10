## 很干的干货
我们直接开始

老样子，我们先来看源码

## 源码

```js
'use strict'

/**
 * Expose compositor.
 */

module.exports = compose

/**
 * Compose `middleware` returning
 * a fully valid middleware comprised
 * of all those which are passed.
 *
 * @param {Array} middleware
 * @return {Function}
 * @api public
 */

function compose (middleware) {
	  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
	  for (const fn of middleware) {
			if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
	  }

	  /**
	   * @param {Object} context
	   * @return {Promise}
	   * @api public
	   */

	  return function (context, next) {
			// last called middleware #
			let index = -1
			return dispatch(0)
			function dispatch (i) {
				  if (i <= index) return Promise.reject(new Error('next() called multiple times'))
				  index = i
				  let fn = middleware[i]
				  if (i === middleware.length) fn = next
				  if (!fn) return Promise.resolve()
				  try {
						return Promise.resolve(fn(context, dispatch.bind(null, i + 1)))
				  } catch (err) {
						return Promise.reject(err)
				  }
			}
	  }
}
```
老样子，我们依旧看不懂源码😜

我们看不懂怎么办呢

## 看测试

俗话说的好，鲁迅曾经说过：<font color=#ff0000>好的测试是一切的开始</font>

- [ ] ![](https://s2.loli.net/2022/06/10/ITeqc4QgmEyN8bs.jpg)

既然鲁迅先生都这么说了

那我们就来看看测试代码吧

测试代码有一大串，我们就不一一看了

我们直接把最重要的拎出来

那么这时候有聪明的小伙伴要问了

什么才是最重要的呢

### 方法教学

我在这里教你一个方法

一般来说，第一个都是最重要的，是一切可以运行的根本

或者我们可以看看it后面的描述

比如这个的第一个it后面的描述是

`it should work` 翻译过来就是

`它应该工作`，所以我们可以看出来，第一个it测试函数是整个代码的核心逻辑

那我们来看看第一个测试的代码

```js
// 这里先把前置条件给拿出来，这个是接下来的wait函数
function wait (ms) {
	  return new Promise((resolve) => {
			//console.log(resolve)
			setTimeout(resolve, ms || 1)
	  })
}

it('should work', async () => {
		const arr = []
		const stack = []

		stack.push(async (context, next) => {
			  arr.push(1)
			  await wait(1)
			  await next()
			  await wait(1)
			  arr.push(6)
		})

		stack.push(async (context, next) => {
			  arr.push(2)
			  await wait(1)
			  await next()
			  await wait(1)
			  arr.push(5)
		})

		stack.push(async (context, next) => {
			  arr.push(3)
			  await wait(1)
			  await next()
			  await wait(1)
			  arr.push(4)
		})

		await compose(stack)({})	
		expect(arr).toEqual(expect.arrayContaining([1, 2, 3, 4, 5, 6]))
})
```

接下来我们在控制台输入

```bash
npm run test
```

来进行测试

在开始之前呢，我先来讲讲测试的两个简单的基础用法

#### 测试的一些小技巧

我们可以先在it后面加上一个only来只执行这一个测试

```js
it.only('should work', async () => {})
```

也可以在it后面加上一个skip来跳过这个测试

```js
it.skip('should work', async () => {})
```

有了这两个杀手锏，你就可以随便测试哪一个测试了

### 三个部分

那我们就来看第一个测试，第一个测试一共有三个部分

分别是
```bash
1. 准备条件
2.入口函数
3.测试断言
```

![三个点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110201122391.jpg)

老样子，我们在正餐之前，先来尝尝饭前甜点

我们先来讲准备条件和测试断言

#### 准备条件

顾名思义，是准备的条件，就是待会儿会用到的东西

#### 测试断言

就是我们的测试结果，应该是怎么样的

来根据测试断言来看看是否与我们预期中的一致

#### 入口函数

最后我们来讲讲入口函数

所谓的入口函数就是我们要调试的函数的入口

那么我们就在这一行打个断点试一下吧

![断点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110201128919.png)

在对应的地方打上断点

执行之前给到的测试运行代码，忘了的同学往前面翻一翻

现在断点就到了我们这里

![断点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110201331670.png)

我们往下走

## compose

compose，接下来我们进入到这个函数

![函数](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110201334880.png)

显示进行了一番判断

如果传入的不是函数数组的话，就给他返回一个类型错误

这一点的话是怕别人乱传参，从而进行了一个限制

接下来返回的是一个匿名函数，匿名函数在外部直接执行

```js
await compose(stack)({})

// 等价于

let fn =  compose(stack)
fn({})
```

那么返回出来的函数内部是以下代码

```js
let index = -1
return dispatch(0)
function dispatch (i) {
	  if (i <= index) return Promise.reject(new Error('next() called multiple times'))
	  index = i
	  let fn = middleware[i]
	  if (i === middleware.length) fn = next
	  if (!fn) return Promise.resolve()
	  try {
			return Promise.resolve(fn(context, dispatch.bind(null, i + 1)))
	  } catch (err) {
			return Promise.reject(err)
	  }
}
```

## dispatch函数

这个函数以上就是一个招数，康康你有没有多次调用next函数

如果有，那么不好意思，给你来个错误，我不和你玩
```js
if (i <= index) return Promise.reject(new Error('next() called multiple times'))
```

之后更新一下index

```js
 index = i
```

接下来在把middleware的值赋给fn

如果middlewar的值已经用完了了

那么去判断函数是不是真的用完了

用完了就把之前外部的那个匿名函数那里接过来参数next

然后去resolve掉它（即给Promise的值返回一个成功）

如果没用完函数的话，再去调用fn，里面把dispatch函数当做参数穿进去，并且更新一下传入的i

去进行递归，这就是洋葱模型

## 洋葱模型浅析

那什么究竟是洋葱模型呢？

在[`koa`的文档](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fkoajs%2Fkoa%2Fblob%2Fmaster%2Fdocs%2Fguide.md%23writing-middleware "https://github.com/koajs/koa/blob/master/docs/guide.md#writing-middleware")上有个非常代表性的中间件 `gif` 图。

![什么是洋葱模型](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110201356558.webp)

而`compose`函数作用就是把添加进中间件数组的函数按照上面 `gif` 图的顺序执行。

简化抽象一下，洋葱模型大概长这样

![洋葱模型](https://s2.loli.net/2022/06/10/4SalIsmNV6BtnQq.webp)

这里我们写一个小的Demo，来方便大家理解什么洋葱模型

```js
const Koa = require('koa');

const app = new Koa();
const PORT = 3000;

// #1
app.use(async (ctx, next)=>{
    console.log(1)
    await next();
    console.log(1)
});
// #2
app.use(async (ctx, next) => {
    console.log(2)
    await next();
    console.log(2)
})

app.use(async (ctx, next) => {
    console.log(3)
})

app.listen(PORT);
console.log(`http://localhost:${PORT}`);
```

访问`http://localhost:3000`，控制台打印：
```bash
1
2
3
2
1
```

即执行到next的时候吧，去到下一个函数里面运行

当内部的函数运行完毕之后，在去运行外部剩余的函数

## 感悟

输出就是巩固理解的方式，写文章虽然痛苦，但是人的成长都是逃离舒适区，直面痛苦的过程，只有真正的在痛苦中磨炼，方能浴火重生，重新归来！

## 宝剑锋从磨砺出，梅花香自苦寒来