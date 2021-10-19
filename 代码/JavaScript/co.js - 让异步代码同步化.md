## co.js - 让异步代码同步化
## 什么是co.js
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
## 什么是koa
我们的主要核心是 co 所以我们大体上了解一下 koa 是干啥的就行

![啥是koa](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151551746.jpg)

![异步版的express](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109151552116.jpg)

1. koa 是由 express 原班人马打造的
2. 类似于其他中间件

我们可以猜测，koa是一个异步中间件系统

异步版本的 express

因为本身是异步的，所以如果需要执行顺序的话就会出现奇奇怪怪的 bug

那么 co 就给我们提供了这个解决方案
## 开始调试
```bash
# 克隆这个仓库
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

## 源码
话不多说，我们直接来看源码吧


```js

/**
 * slice() reference.
 */

var slice = Array.prototype.slice;

/**
 * Expose `co`.
 */

module.exports = co['default'] = co.co = co;

/**
 * Wrap the given generator `fn` into a
 * function that returns a promise.
 * This is a separate function so that
 * every `co()` call doesn't create a new,
 * unnecessary closure.
 *
 * @param {GeneratorFunction} fn
 * @return {Function}
 * @api public
 */

co.wrap = function (fn) {
  createPromise.__generatorFunction__ = fn;
  return createPromise;
  function createPromise() {
    return co.call(this, fn.apply(this, arguments));
  }
};

/**
 * Execute the generator function or a generator
 * and return a promise.
 *
 * @param {Function} fn
 * @return {Promise}
 * @api public
 */

function co(gen) {
  var ctx = this;
  var args = slice.call(arguments, 1);

  // we wrap everything in a promise to avoid promise chaining,
  // which leads to memory leak errors.
  // see https://github.com/tj/co/issues/180
  return new Promise(function(resolve, reject) {
    if (typeof gen === 'function') gen = gen.apply(ctx, args);
    if (!gen || typeof gen.next !== 'function') return resolve(gen);

    onFulfilled();

    /**
     * @param {Mixed} res
     * @return {Promise}
     * @api private
     */

    function onFulfilled(res) {
      var ret;
      try {
        ret = gen.next(res);
      } catch (e) {
        return reject(e);
      }
      next(ret);
      return null;
    }

    /**
     * @param {Error} err
     * @return {Promise}
     * @api private
     */

    function onRejected(err) {
      var ret;
      try {
        ret = gen.throw(err);
      } catch (e) {
        return reject(e);
      }
      next(ret);
    }

    /**
     * Get the next value in the generator,
     * return a promise.
     *
     * @param {Object} ret
     * @return {Promise}
     * @api private
     */

    function next(ret) {
      if (ret.done) return resolve(ret.value);
      var value = toPromise.call(ctx, ret.value);
      if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
      return onRejected(new TypeError('You may only yield a function, promise, generator, array, or object, '
        + 'but the following object was passed: "' + String(ret.value) + '"'));
    }
  });
}

/**
 * Convert a `yield`ed value into a promise.
 *
 * @param {Mixed} obj
 * @return {Promise}
 * @api private
 */

function toPromise(obj) {
  if (!obj) return obj;
  if (isPromise(obj)) return obj;
  if (isGeneratorFunction(obj) || isGenerator(obj)) return co.call(this, obj);
  if ('function' == typeof obj) return thunkToPromise.call(this, obj);
  if (Array.isArray(obj)) return arrayToPromise.call(this, obj);
  if (isObject(obj)) return objectToPromise.call(this, obj);
  return obj;
}

/**
 * Convert a thunk to a promise.
 *
 * @param {Function}
 * @return {Promise}
 * @api private
 */

function thunkToPromise(fn) {
  var ctx = this;
  return new Promise(function (resolve, reject) {
    fn.call(ctx, function (err, res) {
      if (err) return reject(err);
      if (arguments.length > 2) res = slice.call(arguments, 1);
      resolve(res);
    });
  });
}

/**
 * Convert an array of "yieldables" to a promise.
 * Uses `Promise.all()` internally.
 *
 * @param {Array} obj
 * @return {Promise}
 * @api private
 */

function arrayToPromise(obj) {
  return Promise.all(obj.map(toPromise, this));
}

/**
 * Convert an object of "yieldables" to a promise.
 * Uses `Promise.all()` internally.
 *
 * @param {Object} obj
 * @return {Promise}
 * @api private
 */

function objectToPromise(obj){
  var results = new obj.constructor();
  var keys = Object.keys(obj);
  var promises = [];
  for (var i = 0; i < keys.length; i++) {
    var key = keys[i];
    var promise = toPromise.call(this, obj[key]);
    if (promise && isPromise(promise)) defer(promise, key);
    else results[key] = obj[key];
  }
  return Promise.all(promises).then(function () {
    return results;
  });

  function defer(promise, key) {
    // predefine the key in the result
    results[key] = undefined;
    promises.push(promise.then(function (res) {
      results[key] = res;
    }));
  }
}

/**
 * Check if `obj` is a promise.
 *
 * @param {Object} obj
 * @return {Boolean}
 * @api private
 */

function isPromise(obj) {
  return 'function' == typeof obj.then;
}

/**
 * Check if `obj` is a generator.
 *
 * @param {Mixed} obj
 * @return {Boolean}
 * @api private
 */

function isGenerator(obj) {
  return 'function' == typeof obj.next && 'function' == typeof obj.throw;
}

/**
 * Check if `obj` is a generator function.
 *
 * @param {Mixed} obj
 * @return {Boolean}
 * @api private
 */
 
function isGeneratorFunction(obj) {
  var constructor = obj.constructor;
  if (!constructor) return false;
  if ('GeneratorFunction' === constructor.name || 'GeneratorFunction' === constructor.displayName) return true;
  return isGenerator(constructor.prototype);
}

/**
 * Check for plain object.
 *
 * @param {Mixed} val
 * @return {Boolean}
 * @api private
 */

function isObject(val) {
  return Object == val.constructor;
}

```

以上是源码，大体上我们可以给他拆分成几个部分
### 类型判断
首先是判断类型的一些函数

```js
var slice = Array.prototype.slice; // 对数组 slice 方法的引用

// 判断是否为对象
function isObject(val) {
  return Object == val.constructor;
}

// 判断是否为Promise类型
// 判断一个对象是否是一个 promise 实例，判断的依据也很简单，根据 “鸭子类型”，判断这个对象是否有 then 方法
function isPromise(obj) {
  return 'function' == typeof obj.then;
}

// 类似的，判断一个对象时候是 generator 实例，只需判断这个对象是否具有 next 方法和 throw 方法。
function isGenerator(obj) {
  return 'function' == typeof obj.next && 'function' == typeof obj.throw;
}


// 判断是否是一个 generator 函数，只需判断这个函数是否是 `GeneratorFunction` 函数的实例
function isGeneratorFunction(obj) {
  var constructor = obj.constructor;
  if (!constructor) return false;
  if ('GeneratorFunction' === constructor.name || 'GeneratorFunction' === constructor.displayName) return true;
  return isGenerator(constructor.prototype);
}
```

然后就是他的第一行
### 模块输出
co 模块的输出

```js
module.exports = co['default'] = co.co = co
```

可以下三种方法导入

```js
var co = require('co') 	// (1)
var co = require('co').co 	// (2)
var co = require('co').default 	// (3)
```

### 核心代码
OK，我们餐前甜点吃完了，接下来我们就来吃吃正餐

co.js 的核心代码

```js
function co(gen) {
  // 保存函数的执行上下文对象，当你在全局调用的时候，这里的 ctx 就是 Window
  var ctx = this;
  // 传给 gen 函数的参数,把第一个我们的方法给剔除掉
  var args = slice.call(arguments, 1);

  // 返回一个 Promise 实例
  return new Promise(function(resolve, reject) {
	// 把参数传递给gen函数并执行
    if (typeof gen === 'function') gen = gen.apply(ctx, args);
	  // 如果不是函数 直接返回
    if (!gen || typeof gen.next !== 'function') return resolve(gen);

	// 执行 onFulfilled 方法
    onFulfilled();  

    function onFulfilled(res) {
      var ret;
	  // 异常处理
      try {
		// 执行 gen 的 next 方法
        ret = gen.next(res);
      } catch (e) {
        return reject(e);
      }
		// 并将这个值传入 next 函数
      next(ret);
      return null;
    }

	  // 错误处理
    function onRejected(err) {
      var ret;
      try {
        ret = gen.throw(err);
      } catch (e) {
        return reject(e);
      }
      next(ret);
    }

    function next(ret) {
	  // 判断是否是最后一个函数
	  // 如果 gen 执行完毕， ret.done 变为 true ，那么这个 promise 的实例
      if (ret.done) return resolve(ret.value);
	  // 将 value 重新包装成一个 promise 实例
      var value = toPromise.call(ctx, ret.value);
	  // 新返回的 promise 实例的 resolve 方法设置为 onFulfilled 函数，再次执行 next 方法, 从而实现了自动调用 generator 实例的 next 方法
      if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
      return onRejected(new TypeError('You may only yield a function, promise, generator, array, or object, '
        + 'but the following object was passed: "' + String(ret.value) + '"'));
    }
  });
}
```

可能看到这里，你看的是一头雾水

#### 知识点：函数生成器

那么没关系，我们先了解一下一个 ES6 的一个知识点

```js
function* fn() {
	console.log(1);
	//暂停！
	yield;
	//调用next方法继续执行
	console.log(2);
}
var iter = fn();
iter.next(); //1
iter.next(); //2
```
1、函数生成器特点是函数名前面有一个‘*’

2、通过调用函数生成一个控制器

3、调用next()方法开始执行函数

4、遇到yield函数将暂停

5、再次调用next()继续执行函数

##### 消息传递

 除了暂停和继续执行外，生成器同时支持传值。

用法如下：

 ```js
  function* fn() {
	var a = yield 'hello';
	yield;
	console.log(a);
}
var iter = fn();
var res = iter.next();
console.log(res.value); //hello
iter.next(2);
iter.next(); //2
```

可以看到，yield后面有一个字符串，在第一次调用next时，暂停在这里且返回给了iter.next()。

而暂停的地方是一个赋值语句，需要一个变量给a，于是next()方法中传了一个参数2替换了yield，最后打印a得到了2。

##### 异步应用

通过yield来实现异步控制流程：
   ```js
 function fn(a, b) {
        //假设这是一个ajax请求
        ajax('url' + a + b, function(data) {
            //数据请求到会执行it.next
            it.next(data);
        });
    }
    //这里是函数生成器
    function* g() {
        //当异步操作完毕yield会得到值
        //这里会自动继续执行
        var text = yield fn(a, b);
        console.log(text);
    }
    var it = g();
    it.next();
```

这里做了简化处理，忽略了一些错误处理。

确实很巧妙，通过回调函数来继续执行函数生成器，然后得到数据。

然而，直接在回调里拿数据不行么。书上讲，这样异步操作符合大脑思考模式，函数的执行看起来‘同步’了。

### 简化co.js
首先，co的核心是函数生成器

```js
function* myName(){
	 const res = yield new Promise(resolve) => {
		 setTimeout(() => {
			 resolve({name: 'Vixcity'});
		 }, 1000);
	 });
	 console.log(res.name, 'myNameRes');
	// Vixcity
 }

function coSimple(gen){
	 // 调用函数生成器
	 gen = gen();
	// 输出gen，看看是什么
	 console.log(gen, 'gen');

	 const ret = gen.next();
	 const promise = ret.value;
	 promise.then(res => {
		 console.log(res,'res')
		 // {name:'Vixcity'}
			 gen.next(res);
	 });
 }

 coSimple(generatorFunc);
```
![gen的输出](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110191110766.png)

以上是一个 yield 的情况

那么如果我们是两个的情况呢

那我们就重新调用一下 promise.then
```js
// 多个yeild，传参情况  
function* generatorFunc(suffix = ''){  
	const res = yield request();  
	console.log(res, 'generatorFunc-res' + suffix);  

	const res2 = yield request();  
	console.log(res2, 'generatorFunc-res-2' + suffix);  
}  
  
function coSimple(gen){  
	const ctx = this;  
	const args = Array.prototype.slice.call(arguments, 1);  
	gen = gen.apply(ctx, args);  
	console.log(gen, 'gen');  

	const ret = gen.next();  
	console.log(ret,'ret')
	// ret = {value: Promise, done: false}
	// 这个里面的 done 是来判断他是否执行完毕的一个依据
	const promise = ret.value;  
	// 重新调用 promise.then
	promise.then(res => {  
		const ret = gen.next(res);  
		console.log(ret,'ret')
		//ret = {value: Promise, done: false}
		const promise = ret.value;  
		promise.then(res => {  
			gen.next(res);  
		});  
	});  
}  
  
coSimple(generatorFunc, '我是后缀');
```

以上，如果是多个的话，那么我们只需要调用多次 promise.then

那么我们就来封装一下这个 promise

```js
 function* generatorFunc(suffix = ''){
	 const res = yield request();
	 console.log(res, 'generatorFunc-res' + suffix);

	 const res2 = yield request();
	 console.log(res2, 'generatorFunc-res-2' + suffix);

	 const res3 = yield request();
	 console.log(res3, 'generatorFunc-res-3' + suffix);

	 const res4 = yield request();
	 console.log(res4, 'generatorFunc-res-4' + suffix);
 }

  

 function coSimple(gen){
	 const ctx = this;
	 const args = Array.prototype.slice.call(arguments, 1);
	 gen = gen.apply(ctx, args);
	 console.log(gen, 'gen');

	   return new Promise((resolve, reject) => {
		   // 最开始先调用一下
		 onFulfilled();
		   
		 function onFulfilled(res){
			 const ret = gen.next(res);
			 next(ret);
		 }

		 function next(ret) {
			 const promise = ret.value;
			 console.log(promise)
			 console.log(ret.done)
			 // 这里可以用 promise 来判断，也可以用 ret.done 来判断
 			 ret.done || promise.then(onFulfilled);
			 // promise && promise.then(onFulfilled);
		 }
	 });
 }

 coSimple(generatorFunc, ' 我是后缀');
```

现在我们在回过头去看一眼，co 的源码是不是会好理解很多呢🤪

嘿嘿，天天看源码，就是好好学习，天天向上啦，我们一起加油吧