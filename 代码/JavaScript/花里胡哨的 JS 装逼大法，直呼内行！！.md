## 花里胡哨的 JS 装逼大法，直呼内行！！
## 1、最简单的单行评级组件  

```js
"★★★★★☆☆☆☆☆".slice(5 - rate, 10 - rate);
```

定义一个变量 rate 是 1 到 5 的值，然后执行上面代码，如下：

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109181400180.webp)

这才发现其他插件都弱爆了呀！

## 2、JS 错误处理的方式的正确姿势（直接定位错误原因）

```js
try {
	// something
} catch (e) {
	// 直接去 stackoverflow 去搜索哈哈哈
	window.location.href ="http://stackoverflow.com/search?q=[js]+"+e.message;
}
```

  

## 3、从一行代码里面学点 JavaScript（如何快速知道所有元素边框）

```js
[].forEach.call(
	// 获取所有元素
	$$("*"),
	function(a){
		// 给他们加上外边框
		a.style.outline="1px solid #"+(~~(Math.random()*(1<<24))).toString(16)
	}
)
```

换成另一种比较常见的写法就是：

```js
Array.prototype.forEach.call(
	document.querySelectorAll( * ), 
	dom => dom.style.outline = `1px solid #${parseInt(Math.random() * Math.pow(2,24)).toString(16)}`)
```

接下来在浏览器控制看看：

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109181406919.webp)

## 4、史上最快清除缓存方法  

```js
<a href="javascript:alert( '清除成功' );">清除缓存</a>
```

## 5.史上最快清除清除控制台输出方法

```js
console.clear()
```