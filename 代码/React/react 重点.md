# react 重点

- JSX 和 组件
- Hooks - 钩子
- 路由
- 表单组件
- Ajax 网络请求
- 状态管理

## 仿问卷星 - 难点

- 问卷编辑器
- 性能优化
- 单元测试

## React 核心价值

- 组件化
- 数据驱动视图
  - UI = f(state)

## 脚手架

```bash
npx create-react-app yourProjectName --template typescript

# 或者

npm create vite@latest yourProjectName --template react-ts
```

## 运行

```bash
npm start
```

[http://localhost:3000](http://localhost:3000/)

## 测试

```bash
npm test
```

## 打包

```bash
npm run build
```

## 初始化时的一些坑

- 安装 eslint
  - [https://www.jb51.net/article/253083.htm](https://www.jb51.net/article/253083.htm)

## husky

- 一个 git hook 工具
- 在 git commit 之前执行自定义的命令
- 如执行代码风格的检查，避免提交非规范代码
- [详情请看这里](https://github.com/typicode/husky)

## commitlint

git commit 的时候规范提交的格式

[详情请看这里](https://github.com/conventional-changelog/commitlint)

安装

```bash
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

配置

```bash
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js

npx husky add .husky/commit-msg  'npx --no -- commitlint --edit ${1}'
```

例子

```bsah
git commit -m "chore: commit-lint"
```

有以下这些主题
- build
- chore
- ci
- docs
- feat
- fix
- perf
- refactor
- revert
- style
- test

编译报错看下面
[https://www.saoniuhuo.com/question/detail-2279183.html](https://www.saoniuhuo.com/question/detail-2279183.html)