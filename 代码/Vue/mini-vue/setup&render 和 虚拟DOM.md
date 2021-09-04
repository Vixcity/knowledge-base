数据是 reactive 例子当中A，视图（view）是上个案例当中的view

但是响应式对象和 reactive 这些个方法不能暴露给用户，之前是全部给了用户，现在我们需要给他进行封装

创建一个中间件，然后进行响应式更新，把 effectWatch 、setup 、append 单独抽离出来
```js
import { effectWatch } from "./reactivity";
export function createApp(rootComponent) {
	 return {
		 mount(rootComponent) {
			 const context = rootComponent.setup();
			 effectWatch(() => {
			 	 // 这个是把原有的节点给清空
				 rootContainer.innerHTML = ``;  
				 const element = rootComponent.render(context);  
				 rootContainer.append(element);
			 })
		 },
	 };
}
```

templete 会先被转译成虚拟 DOM 节点，然后再把虚拟 DOM 节点转译成 render 函数，所以我们在真实的用法里面就不需要写 render 函数了

但是目前这种写法，当数据每次发生变化的时候，我们就需要给他重新绘制一遍，性能消耗较大，也就可能导致加载的时候会出现白屏，加载事件过长等症状

VDOM 出现是为了解决后面进行虚拟 DOM 的算法，也就是 diff 算法

# 我们来处理一下流程

```bash 
1. 在 HTML 页面引用 index.js 文件
2. 然后 index.js 引入 APP.js 文件 和 ./core/index.js 文件
3. App 文件就是用户的文件，return 出来一个对象，对象里面有 render 和 setup 函数
4. 通过 createApp 函数把 App 的对象让他调用 mount 函数
5. mount 函数执行 App 传入对象中的 setup 方法
6. 然后 setup 方法创建一个响应式对象，并 return 出去
7. 当他 return 出去之后，去执行一个 effectWatch 函数，让他去完成响应式对象的相应
8. 这时候去把页面的元素清空掉，拿到刚刚 setup return 出来的对象
9. 去执行 render 函数，把刚刚的通过 setup 拿到的响应式对象传进去
10. render 函数里面调用了 h 函数，h 函数把 render 函数传入的参数格式化
11. 这时候就可以或得到一个虚拟 DOM 的 JS 对象了
12. 有了这个对象，我们把它通过 mountElement 函数去执行，转成真实的对象
```
# 简化版流程
```bash
1. 创建一个含有 render 和 setup 函数的对象，return 出去
2. 然后 createApp 调用 mount ，mount 调用之前对象里面的 setup 方法，创建响应式对象
3. 然后再 effectWatch 函数里面清空之前的元素，调用 render 方法传入响应式对象
4. render 里面通过 h 函数进行格式化对象，得到对应的虚拟 DOM
5. 把虚拟 DOM 转成真实 DOM 节点，渲染到页面上
```
# 精简版流程 
```bash
1. setup 创建响应式对象
2. effectWatch 响应方法 => 创建虚拟 DOM
3. 拿到虚拟 DOM ，转换成为真实 DOM 
```