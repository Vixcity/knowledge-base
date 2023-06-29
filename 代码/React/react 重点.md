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

## immer

```bash
npm install immer
```

## classnames

```bash
npm install classnames
```

为了防止 CSS 命名重复而导致的样式冲突

我们可以用 CSS Module 的写法

文件名是 xxx.module.css

引用是用的

```ts
import style from "./QuestionCard.module.css";

<div className={style["list-item"]}></div>
```

如果你这样子写引用的话报错，那么可以在 src 根文件里面添加 global.d.ts 文件

里面加上这句话，报错就得以解决了

```ts
declare module "*.module.css";
```

## 使用 SASS

```bash
npm i sass --save
```

如果要使用 SASS Module 和之前安装的 classname 库一起使用，可以参考如下写法

```ts
const listItemClass = styles["list-item"];
  const publishedClass = styles.published;
  const itemClassName = classnames({
    [listItemClass]: true,
    [publishedClass]: isPublished,
  });
```

## CSS in JS

这是一种解决方案，而不是一种工具名称，有好几个工具

带来了很大的灵活性，和内联 style 完全不一样，也不会有内联 style 的问题

**优点：** 用 JS 写，有逻辑有变量，非常灵活
**缺点：** JSX 和样式代码混在一块儿，代码较多，增加了编译成本
**试用场景：** 需要灵活变换样式

### styled-components

```bash
npm install styled-components
```

```ts
// 写法
import React, { FC } from "react";
import styled, { css } from "styled-components";

// 这里面的 button，div，css 都是函数，函数可以通过反引号进行调用，所以我们传入的这些都是参数
const Button = styled.button<{ primary?: boolean }>`
  background: transparent;
  border-radius: 3px;
  border: 2px solid #bf4f74;
  color: #bf4f74;
  margin: 0 1em;
  padding: 0.25em 1em;
  ${(props) =>
    props.primary &&
    css`
      background: #BF4F74;
      color: white;
    `}
`;
  
const Container = styled.div`
  text-align: center;
`;

const styleComponentsDemo: FC = () => {
  return (
    <div>
      <p>style-components-demo</p>
      <Container>
        <Button>Normal Button</Button>
        <Button primary>Primary Button</Button>
      </Container>
    </div>
  );
};

export default styleComponentsDemo;
```

[点击查看官网地址](https://styled-components.com/)

## 路由

- 首页 `/`
- 登录 `/login`
- 注册 `/register`
- 404
- 问卷管理
  - 我的问卷 `/manage/list`
  - 星标问卷 `/manage/star`
  - 回收站 `/manage/trash`
- 问卷详情
  - 编辑 `/question/edit:id` （动态路由）Restful API
  - 问卷统计 `/question/stat:id`

## Layout 模板

- MainLayout
- ManageLayout
- QuestionLayout

## 报错

1. 编译报错看下面
[https://www.saoniuhuo.com/question/detail-2279183.html](https://www.saoniuhuo.com/question/detail-2279183.html)

2. `# This expression is not callable. Type ‘typeof import(“xxx“)‘ has no call signatures.ts(2349)`

原因：
```typescript
import marked from 'marked'
```

以上是导入方式的错误  

修改：
```typescript
import {marked} from 'marked'
```