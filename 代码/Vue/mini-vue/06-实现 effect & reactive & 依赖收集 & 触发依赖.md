由于我们实现的逻辑是通过单测去驱动代码 TDD（测试驱动开发）
 
 所以我们在这里首先要写的是 单测（单元测试）
 
 ```ts
 describe("effect", () => {
	 it("happy path", () => {
		 const user = reactive({
		 	age: 10,
		 });

		 let nextAge;
		 effect(() => {
			 nextAge = user.age + 1;
		 });
		 expect(nextAge).toBe(11);

		 // updata
		 user.age++
		 expect(nextAge).toBe(12);
	 });
});
 ```
 
 我们在这里先简单梳理一下响应式对象的整体流程
 
 ![整体流程](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109081623907.jpg)
 
 我们现在有一个 user 响应式对象，然后响应式对象里面有个容器，通过 effect 去收集依赖（响应式对象变化的之后执行对应的函数）
 
 在 effect 里面会接收一个 fn，因为一上来会先调用一下 fn
 
 这里触发了响应式对象的 get 操作
 
 触发 get 的时候，响应式对象就会把 fn 存进去
 
 这一步的动作做叫做依赖收集
 
 当触发 set 操作的时候，他会把之前收集起来的 fn 的依赖拿出来调用一下他
 
 这一部分叫做触发依赖
 
 因为我们这一部分单测里面会涉及到两部分内容，一部分是 reactive 一部分是 effect
 
 算一个比较大的测试了，所以我们应该给他拆开步骤来进行实现
 
 这是一个任务拆分的思想
 
 所以我们现在创建一个 reactive 的单测
 ```ts 
 describe("reactive", () => {
	 it.skip("happy path", () => {
		 const original = { foo: 1 }
		 const observed = reactive( original )
		 expect(observed).not.toBe(original)
		 expect(observed.foo).toBe(1)
	 });
 });
 ```
 
 reactive 的本质就是通过 Proxy 去做的一个代理，去拦截
 
 我们在 reactivity 文件夹下面新建一个 reactive.ts 文件
 
 ```ts
 export function reactive(raw) {
	 return new Proxy(raw, {
		 get(target, key) {
			const res = Reflect.get(target, key);
		 	
			// TODO 依赖收集
		 	return res;
		 },

		 set(target, key, value) {
			 const res = Reflect.set(target, key, value);
			 // TODO 触发依赖
			 return res;
		 },
	 });
}
 ```
 
 在建一个 effect.js 文件
 ```ts
 // 把抽象的类抽离出来
 class ReactiveEffect{
	 private _fn: any

	constructor(fn) {
		 this._fn = fn
	 }

	 run(){
	 	this._fn()
	 }
}

export function effect (fn){ 
	 const _effect = new ReactiveEffect(fn)
	 _effect.run()
}
 ```
 把 user.age++ 注释掉这样子测试是通过的
 
 那么接下来我们就需要依赖收集以及触发依赖了
 
 我们要收集依赖，那么我们就需要一个容器去存放这个依赖
 
 且对应映射关系为
 
 一个 target -> key -> dep
 
 在 effect 全局上 new 一个 Map（targetMap） 然后把 key 和 dep 给他一一对应起来，收集起来
 
 然后在用一个 trigger 方法去拿到他对应的对象里面，对应的方法去执行
 ```ts
const targetMap = new Map();
export function track(target, key) {
	 // set 构建容器
	 // 一个target -> key -> dep
	 
	 let depsMap = targetMap.get(target);
	 if (!depsMap) {
		 depsMap = new Map();
		 targetMap.set(target, depsMap);
	 }

	 let dep = depsMap.get(key);
	 if (!dep) {
		 dep = new Set();
		 depsMap.set(key, dep);
	 }
	 dep.add(activeEffect);
}

  

export function trigger(target, key) {
	 let depsMap = targetMap.get(target);
	 let dep = depsMap.get(key);

	 for (const effect of dep) {
		 effect.run();
	 }
}
 ```
 
 