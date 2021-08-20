 # JSX语法规则

 1. 定义虚拟DOM时，不要写引号

 2. 标签如混入JS表达式，需要用{}

 3. 样式类名不要用class，要用className

 4. 内联样式要用style = {{key:value}} 的形式来写

 5. 虚拟只能有一个根节点

 6. 标签必须闭合

 7. 关于标签首字母
 
	1. 若小写字母开头，则将该标签转为HTML中同名元素，若HTML中无同名元素，大写字母开头
	
	2. 若大写字母开头，React 就会去渲染对应的组件，若组件未定义，则报错

 8. jsx 里面的直接创建虚拟DOM 是一种语法糖
  	- 转义后生成的JS代码为 
  	``` javascript
	React.createElement('h1',{id:'title'},React.createElement('span',{},'Hello React!'))
	```
