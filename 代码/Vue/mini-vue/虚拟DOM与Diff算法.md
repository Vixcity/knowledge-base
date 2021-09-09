diff 算法实现最小的更新

来个变量 isMounted 来代表是否被更新过，如果被更新了

在来个 prevSubTree 来收藏之前的老的树

diff 算法是一个双端对比

更改的可能有三个地方

```bash 
1. props
2. tag
3. children
```

tag 标签变了可以直接替换

props 变换了有以下三种可能

```bash 
1. 更改了某个值
2. 增加了某个值
3. 删除了某个值
```

children 变换了有以下四种可能

```bash 
1. 新的 newChildren == string ( oldChildren == string , oldChildren == array)
2. 新的 newChildren == array ( oldChildren == string , oldChildren == array)
```

那么如果 children 中两个都是 array 的话，又有以下三种情况

```bash
1. 判断他俩等长的部分
2. 老的比新的长，删除
3. 新的比老的长，增加
```