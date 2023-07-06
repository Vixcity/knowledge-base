# webpack 是什么

- webpack 是一个非常流行的前端打包工具
- Create-React-App 内部使用 webpack 进行打包
- [[vite]] 既是构建工具，又是打包工具
- 打包的时候是全部整合到一个文件里面

## 扩展 webpack 配置

### craco.js

**安装**

```bash
npm i -D @craco/craco
```

[详情请看官网](https://github.com/gsoft-inc/craco)

如果你遇到了 504 报错

可以尝试把 `localhost:3001` 改成 `0.0.0.0:3001`