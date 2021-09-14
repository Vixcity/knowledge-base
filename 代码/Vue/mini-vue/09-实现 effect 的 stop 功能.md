执行 stop 可以阻止响应式对象发生变化

我们有一个响应式对象

有一个 effect ，传入了一个 fn 

这个 fn 被收集在 deps 里面，当他发生改变的时候，拿出依赖执行一遍

当如果我们想要 deps 发生改变不通知依赖执行的时候，要删除对应的 effect 的依赖

这个就是 stop 的操作

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109141516062.jpg)

测试用例

```ts
import { reactive } from "../reactive";
import { effect, stop } from "../effect";

it("stop", () => {
	 let dummy;
	 const obj = reactive({ prop: 1 });
	 const runner = effect(() => {
		 dummy = obj.prop;
	 });

	 obj.prop = 2;
	 expect(dummy).toBe(2);
	 stop(runner);
	 obj.prop = 3;
	 expect(dummy).toBe(2);

	 // stopped effect should still be manually callable
	 // 停止效果后仍然可以手动调用
	 runner();
	 expect(dummy).toBe(3);
});
```

TDD 的三个动作

什么是 TDD 可以参看下面这篇文章

[[00.预习#TDD https baike baidu com item TDD 9064369 fr aladdin]]

```bash
1. 先写一个测试
2. 让测试通过
3. 重构，让代码保持可读性
```

还要考虑性能

不能让 stop 执行多次

给个标识说明他被执行过了