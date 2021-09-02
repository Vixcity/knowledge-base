# @vue/reactivity
首先，代码奉上
```js
// Vue 原生响应式库 reactivity，我们现在来写一下这个库
// const { effect } = require("@vue/reactivity");

// 放个变量收集一下依赖
let currentEffect;

class Dep {
	 constructor(val) {
		 // es6+ 语法
		 this.effects = new Set();
		 this._val = val
	 }

	 get value(){
		 this.depend()
		 return this._val
	 }

	 set value(newValue){ 
		 this._val = newValue
		 this.notice()
	 }

	 // 1. 收集依赖
	 depend() {
		 if (currentEffect) {
		 	this.effects.add(currentEffect)
		 }
	 }

	 // 2. 触发依赖
	 notice() {
		 // 触发一下我们之前收集到的依赖
		 this.effects.forEach((effect) => {
		 	effect()
		 })
	 }
}

export function effectWatch(effect) {
	 // 收集依赖
	 currentEffect = effect;
	 effect()
	 currentEffect = null;
} 

// const dep = new Dep(10);
// let b;
 
// effectWatch(() => {
//     b = dep.value + 10
//     console.log(b);
// });  

// 值发生变更
// dep.value = 20
  
// reactive
// 一个key对应一个dep
// dep -> number or string
// obj -> key -> dep

// 怎么实现
// 1. 要知道对象是什么时候改变的
// obj.a -> get 
// obj.a = 2 -> set

// vue2响应式对象实现是通过
// proxy

// vue3
const targetMap = new Map()
function getDep(target,key) { 
	 let depsMap = targetMap.get(target)
	 if(!depsMap){
		 depsMap = new Map();
		 targetMap.set(target,depsMap);
	}

	let dep = depsMap.get(key)

	if(!dep){
		 dep = new Dep();
		 depsMap.set(key,dep);
	 }
 	
	return dep
}

  

export function reactive (raw) { 

	 return new Proxy(raw,{
		 get(target,key){
			 // key ==> def 一个key对应一个dep
			 // dep 我们存储在哪里
			 const dep = getDep(target,key);

			 // 依赖收集
			 dep.depend();

			 // return target[key] === return Reflect.get(target,key) 
			 return Reflect.get(target,key);
		 },

		 set(target,key,value){
			 // 触发依赖
			 // 要获取到dep对象
			 const dep = getDep(target,key);
			 const result = Reflect.set(target,key,value);
			 dep.notice();
			 return result
		 }
	 })
}

// const user = reactive({
//     age:19
// })

// let double;

// effectWatch(() => {
//     console.log("-------reactive------")
//     double = user.age 
//     console.log(double)
// })

// user.age = 20
```

整体分为三大块
```bash
1. 一个是 dep 类，depend 为收集依赖，notice 为触发依赖
2. 一个是 effectWatch ，是让外面的相应方法到这里面来
3. 一个是 reactive 容器，储存外面的对象，并让他与我们的响应方法关联
```
我们先来看 dep 类

里面分别有
- constructor 构造器，如果从外面拿到一个初始值，那么我们给实例对象 new 一个 set 实例， 然后把初始值赋给 _val 属性
- get 在类里面可以去定义可以得到 value 的方法
- set 在类里面可以去定义可以设置 value 的方法
- depend 收集依赖，就是把外面的变更的方法传进来
- notice 触发依赖，就是把外面的传进来的方法执行一下

```js
class Dep {
	 // 这里是构造器，把新 new 的 Dep 实例 
	 constructor(val) {
		 // es6+ 语法
		 this.effects = new Set();
		 this._val = val
	 }

	 get value(){
		 this.depend()
		 return this._val
	 }

	 set value(newValue){ 
		 this._val = newValue
		 this.notice()
	 }

	 // 1. 收集依赖
	 depend() {
		 if (currentEffect) {
		 	this.effects.add(currentEffect)
		 }
	 }

	 // 2. 触发依赖
	 notice() {
		 // 触发一下我们之前收集到的依赖
		 this.effects.forEach((effect) => {
		 	effect()
		 })
	 }
}
```

然后是 effectWatch 函数，这个函数是实现从外面拿到响应式函数，赋值给全局对象 currentEffect 并且执行拿到的函数

```js
export function effectWatch(effect) {
	 // 收集依赖
	 currentEffect = effect;
	 effect()
	 currentEffect = null;
} 
```

最后是 reactive 函数

上面的 getDep 函数是如果有 depsMap 实例，就给他拿到的对应的 key 值存进去

如果没有 depsMap 实例，就新建一个实例，然后再把对应的值往里边存

getDep 中的 dep 同理，只不过新建的实例为 Dep 实例，把对应的实例 return 出去

当他 return 出去之后，reactive 里面的 proxy 代理对象的 get 属性拿到对应的实例

然后执行一下收集依赖的操作

set 函数则是执行触发依赖的操作

```js
const targetMap = new Map()
function getDep(target,key) { 
	 let depsMap = targetMap.get(target)
	 if(!depsMap){
		 depsMap = new Map();
		 targetMap.set(target,depsMap);
	}

	let dep = depsMap.get(key)
	if(!dep){
		 dep = new Dep();
		 depsMap.set(key,dep);
	 }
 	
	return dep
}

  

export function reactive (raw) { 
	 //Proxy 可以对目标对象的读取、函数调用等操作进行拦截，然后进行操作处理。它不直接操作对象，而是像代理模式，通过对象的代理对象进行操作，在进行这些操作时，可以添加一些需要的额外操作。
	 return new Proxy(raw,{
	 	 // 这个raw 是外面传过来的对象
		 get(target,key){
			 // key ==> def 一个key对应一个dep
			 // dep 我们存储在哪里
			 const dep = getDep(target,key);

			 // 依赖收集
			 dep.depend();

			 // return target[key] === return Reflect.get(target,key) 
			 return Reflect.get(target,key);
		 },

		 set(target,key,value){
			 // 触发依赖
			 // 要获取到dep对象
			 const dep = getDep(target,key);
			 const result = Reflect.set(target,key,value);
			 dep.notice();
			 return result
		 }
	 })
}
```

执行流程

```bash
1. 会先在外面建造容器	
2. 会到 effectWatch 里面执行，当执行到 effect() 的时候，会进入到创建实例类当中
3. 创建实例类执行完
```

解释一下为什么会这样

因为外面先创建了容器，这个容器和 Dep 是挂钩的

接下来外面执行了 例如 b = a + 10 的操作

这一步也要和刚刚容器创建的对象有关联才会进入到 Dep 当中

因为外面的相应函数与 a 对应的 Dep 实例有挂钩

会调用到 a ，也即是 reactive 里面的 get 函数

然后 get 函数会调用到  getDep  函数去判断是否有实例，判断是否能创建新实例

然后再去执行相应的函数操作

>PS：reactive 中的 Proxy 是对目标对象的读取、函数调用等操作进行拦截 ，所以可以找到 get 方法，可以调用到他（这一块我有点迷惑）



