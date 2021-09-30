转换成虚拟节点
component -> vnode 
所有的逻辑操作都会基于vnode

拿到vnode和容器之后，调用render函数，render函数调用patch方法

patch拿到这两个东西之后，就去处理组件了

剩下请看代码