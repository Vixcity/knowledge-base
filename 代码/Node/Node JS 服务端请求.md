### 搭建Http服务器

<http://nodejs.cn/learn/build-an-http-server>

```js
const http = require('http');

const serve = http.createServer((req, res) => {
    // 写响应头的一些信息
    // 会执行两次请求，一次是页面，一次是favicon.ico
    res.writeHead(200, { 'content-type': 'text/html' })
    res.end('<h1>hello world</h1>')
})
  
// 监听了3000端口
serve.listen(3000, () => {
    console.log('监听了3000端口')
})
```

### 实战-简易爬虫

```js
// 简易爬虫
// 链接如果是http就引入http
// 链接如果是https就引入http
const http = require('https');
const fs = require('fs');
// 可以在Node中进行DOM操作，类似JQuery
const cheerio = require('cheerio');
  
http.get('https://vixcity.vercel.app/', (res) => {
    res.setEncoding('utf8')
  
    let html = ''
    res.on('data', chunk => {
        html += chunk
    })
  
    res.on('end', () => {
        console.log(html)
        // 加载html代码，实现JQuery的DOM操作
        const $ = cheerio.load(html)
        // 获取title元素的文本
        console.log($('title').text())
        fs.writeFile('./index.html', html, (err) => {
            if (err) throw err
            console.log('爬虫爬取成功')
        })
    })
})

```

### 获取Get和Post请求

<http://nodejs.cn/api/url.html>

```js
const url = require('url');
const http = require('http');

// 第二个参数为是否解析query
console.log(url.parse('http://nodejs.cn/api/url.html?type=2&asd1=1',true))
  
// 获取GET请求
const serve = http.createServer((req, res) => {
    let urlObj = url.parse(req.url, true)
    console.log(urlObj)
    res.end(JSON.stringify(urlObj.query))
})
  
serve.listen(3000, () => {
    console.log('监听3000端口')
})
  
// 获取POST请求
const serves = http.createServer((req, res) => {
    let postData = ''
    req.on('data', chunk => {
        postData += chunk
    })
  
    req.on('end', () => {
        console.log(postData)
    })
  
    res.end(JSON.stringify({
        data: '请求成功',
        code: 0
    }))
})
  
serves.listen(3001, () => {
    console.log('监听3001端口')
})
  
// 整合post 和 get 请求
const serve3002 = http.createServer((req, res) => {
    if (req.method === 'GET') {
        let urlObj = url.parse(req.url, true)
        console.log(urlObj)
        res.end(JSON.stringify(urlObj.query))
    } else if (req.method === "POST") {
        let postData = ''
        req.on('data', chunk => {
            postData += chunk
        })
  
        req.on('end', () => {
            console.log(postData)
        })
  
        res.end(JSON.stringify({
            data: '请求成功',
            code: 0
        }))
    }
})
  
serve3002.listen(3002, () => {
    console.log('监听3002端口')
})
```