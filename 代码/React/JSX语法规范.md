 # 1.JSX语法规则

 1. 定义虚拟 DOM 时，不要写引号

 2. 标签如混入 JS 表达式，需要用{}

 3. 样式类名不要用 class，要用 className

 4. 内联样式要用 style = {{key:value}} 的形式来写

 5. 虚拟只能有一个根节点

 6. 标签必须闭合

 7. 关于标签首字母
 
	1. 若小写字母开头，则将该标签转为 HTML 中同名元素，若 HTML 中无同名元素，大写字母开头
	
	2. 若大写字母开头，React 就会去渲染对应的组件，若组件未定义，则报错

 8. jsx 里面的直接创建虚拟 DOM 是一种语法糖
  	- 转义后生成的 JS 代码为 
  	``` javascript
	React.createElement('h1',{id:'title'},React.createElement('span',{},'Hello React!'))```
	
# 2.虚拟 DOM
- 虚拟 DOM 本质是 object 的对象，虚拟 DOM 比较轻，真实 DOM 比较重，因为虚拟 DOM 是 react 内部在用，无需真实 DOM 上那么多的属性，虚拟 DOM 最终会被 react 转化成真实DOM，呈现在页面上
# 3.注释
在 JSX 当中，如果要注释标签的话，需要这么写
```javascript 
{/*<h2 onClick = {changeWeather}>今天天气很{isHot?'炎热':'凉爽'}</h2>*/}
```