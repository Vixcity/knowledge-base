React 16 版本之后就可以自定义 Hook 了

内置 Hooks 保证基本功能

内置 Hooks 灵活配合，实现业务功能

抽离公共部分，自定义 Hooks 或者第三方 Hooks -- 复用代码

```ts
import { useEffect } from "react";

function useTitle(title: string) {
  useEffect(() => {
    document.title = title;
  });
}

export default useTitle;
```

# 第三方 Hooks

常见的第三方 Hooks
- ahooks - 国内流行
- react-use - 国外流行

# Hooks 的三条使用规则

1. 命名必须使用 useXxx 来命名
2. 只能在两个地方来调用 Hook （组件内部，其他 Hook 内部）
3. 必须保证每次的调用顺序一致（不能放在 if for 内部）

# 闭包陷阱

- 当异步函数获取 state 时，可能不是当前最新的 state
	- 可以用 useRef 来解决
		- 因为 state 是一个值类型
		- 而 ref 是一个引用类型
	- 要提前了解 JS 的闭包