# NodeJS学习内容

## 项目相关

```bash
# 初始化项目
npm init -y

# 安装依赖
npm i

# 启动项目
node app.js
```

## 依赖

- <https://www.lodashjs.com/>
- <https://www.npmjs.com/package/chokidar>

## 基础知识

```js
// globalThis 定义的对象可以在全局中被访问到
// 例：
globalThis.a = 2
```

## Node文档

### Buffer

<http://nodejs.cn/learn/nodejs-buffers>

### 文件系统模块

<http://nodejs.cn/learn/the-nodejs-fs-module>

```js
const fs = require('fs');
const chokidar = require('chokidar');

// 读取文件
fs.readFile('./helloWorld.txt', 'utf8', (err, data) => {
    if (err) throw err
    console.log(data)
})

// 写入文件 - 内容覆盖
fs.writeFile('./helloWorld.txt', 'this is a test', 'utf8', err => {
    if (err) throw err
    console.log("写入成功")
})

// 追加写入，可以用buffer写，也可以用普通的文本字符写
const buf = Buffer.from('\nHello World!')
fs.appendFile('./helloWorld.txt',buf,err => {
    if(err) throw err
    console.log('追加写入成功')
})

// 获取文件信息 主要用来判断 文件||文件夹
fs.stat('./helloWorld.txt', (err, stats) => {
    if (err) throw err
    console.log(stats.isFile())
    console.log(stats.isDirectory())
})

// 重命名文件
fs.rename('./helloWorld.txt', 'hello.txt', err => {
    if (err) throw err
    console.log('重命名成功')
})

// 删除文件
fs.unlink('./helloWorld.txt', err => {
    if (err) throw err
    console.log('删除成功')
})

// 创建文件夹
fs.mkdir('./testDir', err => {
    if (err) throw err
    console.log('创建文件夹成功')
})

// 递归创建
fs.mkdir('./testDir/testDirSon', {
    recursive: true
}, err => {
    if (err) throw err
    console.log('创建文件夹成功')
})

// 读取文件夹
fs.readdir('./', {
    // 带上文件类型
    withFileTypes: true
}, (err, files) => {
    if (err) throw err
    console.log(files)
})

// 删除文件夹
fs.rmdir('./testDir', {
    // 递归删除，可以删除内部有东西的文件夹
    recursive: true
}, err => {
    if (err) throw err
    console.log('删除文件夹成功')
})

// 监听文件变化
fs.watch('./', {
    // 监视子目录
    recursive: true
}, (eventType, fileName) => {
    console.log(eventType, fileName)
})

// 监听文件变化可以用另一个更好的包
// chokidar
// npm i chokidar --save-dev
chokidar.watch('./', {
    // 不监听
    ignored: './node_modules'
}).on('all', (event, path) => {
    console.log(event, path)
})
```

### 文件流

<http://nodejs.cn/learn/nodejs-streams>

```js
const fs = require('fs');

// 读取流
let rs = fs.createReadStream('./app.js', {
    // 每次 on data 的一个数据量
    highWaterMark: 100
})

let count = 1
rs.on('data', chunk => {
    console.log(chunk.toString())
    console.log(count++)
})

rs.on('end', () => {
    console.log('读取完成')
})


// 写入流
let ws = fs.createWriteStream('./helloWorld.txt')

let num = 0
let timer = setInterval(() => {
    if (num < 10) {
        ws.write(num + '\n')
        num++
    } else {
        ws.end('写入完成')
        clearInterval(timer)
    }
}, 200)

ws.on('finish', () => {
    console.log('写入完成')
})


// 管道流，从数据的来源，通过管道，一段一段的流向目标
let rs = fs.createReadStream('./app.js')
let ws = fs.createWriteStream('./helloWorld.txt')
// 读取流的pipe方法，写入到写入流里面
rs.pipe(ws)
```

### path

<http://nodejs.cn/api/path.html>

```js
const path = require('path');

console.log(path.basename('/app.js','.js')) // app.js 后面的为省略后缀名
console.log(path.dirname('/app.js')) // /
console.log(path.extname('app.js')) // .js
console.log(path.join('/nodeJS-learning/','/app.js')) // 拼接 \nodeJS-learning\app.js
console.log(path.normalize('/nodeJS-learning//app.js')) // 规范化路径 \nodeJS-learning\app.js
console.log(path.resolve('./app.js')) // 绝对路径 C:\Users\47072\Desktop\project\nodeJS-learning\app.js
let pathObj = path.parse("/nodeJS-learning/app.js") // 将路径转化为对象
console.log(pathObj) 
console.log(path.format(pathObj)) // 将对象转化为路径
console.log(path.sep) // 系统路径的分隔符
console.log(path.win32.sep) // window下的分隔符
console.log(__filename) // 当前所在路径绝对目录 是绝对正确的 resolve 执行的地方不同可能是会有错误的
console.log(__dirname) // 当前目录绝对目录
```

### 事件触发器

<http://nodejs.cn/api/events.html>

```js
const EventEmitter = require('events')

class MyEmitter extends EventEmitter { }

let myEmitter = new MyEmitter()

myEmitter.on('hi', (a,b) => {
    console.log('触发了事件',a+b)
})

myEmitter.once('once', (a,b) => {
    console.log('触发了一次',a+b)
})

// 触发事件
myEmitter.emit('hi',1,2)

// 只会触发一次
myEmitter.emit('once',1,2)
myEmitter.emit('once',1,2)

let fn1 = (a,b) => {
    console.log('带参',a+b)
}

let fn2 = () => {
    console.log('不带参')
}

myEmitter.on('hi1', fn1)
myEmitter.on('hi1', fn2)

// 移除数组
// myEmitter.removeListener('hi1',fn1)
// 移除全部数组
myEmitter.removeAllListeners('hi1')
myEmitter.emit('hi1',1,2)
```

### util

<http://nodejs.cn/api/util.html>

```js
const util = require('util');
const fs = require('fs');

async function hello() {
    return 'hello World!'
}

// 将异步函数转换成为有回调风格的函数
let helloCallBack = util.callbackify(hello)

helloCallBack((err, res) => {
    if (err) throw err
    console.log(res)
})

// 转换成为promise版本的函数
let stat = util.promisify(fs.stat)
let stat1 = util.promisify(fs.stat)

stat('./app.js').then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})

async function statFn() {
    try {
        let stats = await stat1('./app.js')
        console.log(stats)
    } catch (e) {
        console.log(e)
    }
}

statFn()

// 判断值是否为date类型
console.log(util.types.isDate(new Date()), util.types.isDate(1))
```

### http基本知识

---

- GET 请求指定的⻚⾯信息，并返回实体主体
- HEAD 类似于get请求，只不过返回的响应中没有具体的内容，⽤于获取报头
- POST 向指定资源提交数据进⾏处理请求。数据被包含在请求体中。
- PUT 从客户端向服务器传送的数据取代指定的⽂档的内容
- DELETE 请求服务器删除指定的⻚⾯
- CONNECT HTTP/1.1协议中预留给能够将连接改为管道⽅式的代理服务器。
- OPTIONS 允许客户端查看服务器的性能
- TRACE 回显服务器收到的请求，主要⽤于测试或诊断

---

| 应答头           | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| Allow            | 服务器⽀持哪些请求⽅法（如get、post等）。                    |
| Content-Encoding | ⽂档的编码⽅法。只有在解码之后才可以得到Content-Type头指定的内容类型。利⽤gzip压缩能减少HTML⽂档的下载时间。 |
| Content-Length   | 表示内容⻓度。只有当浏览器使⽤持久http连接时才需要这个数据。 |
| Content-Type     | 表示⽂档属于什么MIME(文件)类型。                             |
| Date             | 当前的GMT时间。                                              |
| Expires          | 资源什么时候过期，不再缓存，会重新向服务器请求页面。         |
| Last-Modified    | ⽂档最后改动时间。                                           |
| Location         | 重定向的地址。                                               |
| Server           | 服务器的名字。                                               |
| Set-Cookie       | 设置和⻚⾯关联的Cookie。                                     |
| WWW-Authenticate | 定义了使⽤何种验证⽅式去获取对资源的链接。                   |

---

#### 常⻅的http状态码

- 200 请求成功
- 301 资源被永久转移到其他URL
- 404 请求的资源（⽹⻚等）不存在
- 500 内部服务器错误

---

| 分类 | 分类描述                                       |
| :--- | ---------------------------------------------- |
| 1**  | 信息，服务器收到请求，需要请求者继续执⾏操作   |
| 2**  | 成功，操作被成功接收并处理                     |
| 3**  | 重定向，需要进⼀步的操作以完成请求             |
| 4**  | 客户端错误，请求包含语法错误或⽆法完成请求     |
| 5**  | 服务器错误，服务器在处理请求的过程中发⽣了错误 |

---

#### Content-Type 内容类型

常⻅的媒体格式类型如下

- text/html:HTML格式
- text/plain:纯⽂本格式
- text/xml:XML格式
- image/gif:gif图⽚格式
- image/jpeg:jpg图⽚格式
- image/png:png图⽚格式
- multipart/form-data:需要在表单中进⾏⽂件上传时，就需要使⽤该格式

以application开头的媒体格式类型：

- application/xhtml+xml:XHTML格式
- application/xml:XML数据格式
- application/atom+xml:Atom XML聚合格式
- application/json:JSON数据格式
- application/pdf:pdf格式
- application/msword:Word⽂档格式
- application/octet-stream:⼆进制流数据（常⻅的⽂件下载)
- application/x-www-form-urlencoded:表单中默认的encType,表单数据被编码为key/value格式发送到服务器
