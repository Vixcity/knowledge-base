组件函数，每次 state 更新都会重新执行函数

useMemo 可以缓存数据，不用每次执行函数重新生成

可用于计算量较大的场景，缓存提高性能

用法类似[[useEffect]]

```ts
const sum = useMemo(() => {
	console.log("gen sum...");
	return num1 + num2;
}, [num1, num2]);
```

## useCallback

和 useMemo 作用一样

专门用于缓存函数

缓存，性能优化，提升时间效率

不要为了优化而优化