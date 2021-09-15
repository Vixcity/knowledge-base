## 几个JavaScript极短日期时间代码片段（你值得拥有）

## 前言

> 本篇文章主打极短的日期时间相关代码片段，让你不用工具库也能秀的飞起

## 1.是否今天

日期是不是今天，我们只需要判断 `日期的 年月日` 是否与 `当前日期的 年月日`一致即可，所以我们的常规代码片段如下：

```js
function isToday(dt = new Date()) {
    let curDate = new Date() // 当前日期 
    let comparedDate= new Date(typeof dt === 'string'&&dt.includes('-')?dt.replace(/-/g,'/'):dt) // 传入日期
    return curDate.getFullYear() === comparedDate.getFullYear() && // 年相等
           curDate.getMonth() === comparedDate.getMonth() && // 月相等
           curDate.getDate() === comparedDate.getDate() // 日相等
}
```

我知道有小伙伴会说，“就这...！极短？？？”，别急，别急，请你继续往下看

**`isToday`极短代码片段如下**

```js
// isToday 极短代码片段
const isToday =  (dt = new Date())=>['getFullYear', 'getMonth', 'getDate'].every(i => new Date()[i]() === new Date(typeof dt === 'string'&&dt.includes('-')?dt.replace(/-/g,'/'):dt)[i]())
```

使用了提取公因式，把 重复出现的 `getFullYear`, `getMonth`,`getDate` 给提取出来用`every`结合而成

## 2.是否昨天

是否昨天，我们只需把当前日期减一天，再做比较即可，所以我们的常规代码片段如下：

```js
function isYesterday(dt = new Date()) {
    let curDate = new Date() // 当前日期 
    curDate.setDate( curDate.getDate() - 1 ) // 当前日期减一天
    let comparedDate= new Date(typeof dt === 'string'&&dt.includes('-')?dt.replace(/-/g,'/'):dt) // 传入日期
    return curDate.getFullYear() === comparedDate.getFullYear() && // 年相等
           curDate.getMonth() === comparedDate.getMonth() && // 月相等
           curDate.getDate() === comparedDate.getDate() // 日相等
}
```

是否昨天极短代码片段的实现大致和是否今天一样，**不同的是**，首先要定义出昨天具体是哪一天的标准，才能使用传入的日期和标准日期做比较，我们具体操作 是 当前时间戳 减去一天的时间戳即`new Date() \- 24*60*60*1000`,得到一个昨天的标准日期时间戳，然后再做比较

```js
// isYesterday 极短代码片段
const isYesterday =(dt = new Date())=>['getFullYear', 'getMonth', 'getDate'].every(i => new Date(new Date() - 24*60*60*1000)[i]() === new Date(typeof dt === 'string'&&dt.includes('-')?dt.replace(/-/g,'/'):dt)[i]())
```

## 3.是否明天

是否明天，我们只需把当前日期加一天，再做比较即可，所以我们的常规代码片段如下：

```js
function isTomorrow(dt = new Date()) {
    let curDate = new Date() // 当前日期 
    curDate.setDate( curDate.getDate() + 1 ) // 当前日期加一天
    let comparedDate= new Date(typeof dt === 'string'&&dt.includes('-')?dt.replace(/-/g,'/'):dt) // 传入日期
    return curDate.getFullYear() === comparedDate.getFullYear() && // 年相等
           curDate.getMonth() === comparedDate.getMonth() && // 月相等
           curDate.getDate() === comparedDate.getDate() // 日相等
}
```

是否明天极短代码片段的实现和是否昨天相反，是 当前时间戳 加上一天的时间戳即`+new Date() + 24*60*60*1000`,得到一个昨天的标准日期时间戳，然后再做比较

```js
// isTomorrow 极短代码片段
const isTomorrow = (dt = new Date())=>['getFullYear', 'getMonth', 'getDate'].every(i => new Date(+new Date() + 86400000)[i]() === new Date(typeof dt === 'string'&&dt.includes('-')?dt.replace(/-/g,'/'):dt)[i]())
```

## 4.月天数

关于月天数需求，我们大致有：

-   获取当前日期所属月份天数，简称`获取当月天数`
    
-   获取当前日期所在年中的任一月份天数，简称`获取今年任一月天数`
    
-   获取给定日期所属月份天数，简称`获取指定日期的所属月天数`
    
-   获取给定日期所在年中任一月天数，简称`获取指定日期的所属年任一月天数`
    
-   获取年任一月天数
    

我们方法需要两个参数来完成这个功能函数，所以我们的常规代码片段是：

```js
function daysInMonth(month = new Date().getMonth() + 1, dt = new Date()) {
    let is = month >= 1 && month <= 12
    dt = is ? dt : month
    month = is ? month : new Date(month).getMonth() + 1
    //解析传入的日期 dt
    const d = new Date(typeof dt === 'string' && dt.includes('-') ? dt.replace(/-/g, '/') : dt.toString())
    // 设置月份
    d.setMonth(month) // 因为月份是按索引 0-11，索引这里没有进行 - 1
    // 设置日期为0，那么日期就会被设置为上个月的最后一天
    d.setDate(0)
    // 返回上月最后一天日期，因为月份是按索引 0-11
    return d.getDate()
}
```

具体使用：

```js
// 获取当前月份天数  -- 获取当月天数
daysInMonth()
// 获取今年2月份天数 -- 获取今年任一月天数
daysInMonth(2)
// 获取2000年2月份天数
daysInMonth(2, 2000)
// 获取指定时间（2000-01-01 12:23:59）指定月份（2）的天数 -- 获取指定日期的所属年任一月天数
daysInMonth(2, '2000-01-01 12:23:59')
// 获取指定时间（2000-01-01 12:23:59）所属月天数   -- 获取指定日期的所属月天数
daysInMonth('2000-01-01 12:23:59')
```

`setDate(0)`那么日期就会被设置为上个月的最后一天，具体更多可以看 [MDN Date.prototype.setDate](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FDate%2FsetDate "https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setDate")的描述

月天数如何个极短法呢，可读性可能要牺牲点了，不过也还好，借助`new Date(year,month,0).getDate()`进行实现，具体如下：

```js
const daysInMonth = function (month = new Date().getMonth() + 1, dt = new Date()) {
    let is = month >= 1 && month <= 12
    dt = is ? dt : month
    month = is ? month : new Date(month).getMonth() + 1
    let date = new Date(typeof dt === 'string' && dt.includes('-') ? dt.replace(/-/g, '/') : dt.toString())
    const year = date.getFullYear()
    return new Date(year, month, 0).getDate()
}
```

**这个还真优化不了什么，同时又需兼具有以上5个需求使用特征，麻雀虽小，但五脏俱全**

## 5. 格式化

在前端项目中，使用日期时间相关的方法，格式化方法频率比较高，那么我们自己实现一个 `format`方法吧，在实现之前 依然保留传统的使用方法，因为没有我们是单个方法，实现单兵作战高效强悍，实现的时候有些地方我们会做些改变，具体哪些改变请往下看

```js
/**
 * @description: 日期时间格式化函数
 * @param  { Array } args :形参数组，生效的最多为前两个参数
 * 1个参数情况：
 *      1.1 参数为格式，则默认格式化当前时间
 *      1.2 参数为时间戳或字符串时间，则使用默认格式去格式化化给定的 时间戳或字符串时间
 * 2个参数情况：
 * 第一个参数表示格式化的日期，可以是时间戳或字符串时间
 * 第二个参数表示格式
 */
const format = function (...args) {
    try {
        // 参数解构
        const [a, b] = args
        // 默认值
        let dt = new Date(), //默认当前时间
            ft = 'YYYY-MM-DD HH:mm:ss' // 默认格式
        //如果参数只传入了一个的情况，我们认为用户用户传入的是格式，需要格式化的是当前时间
        if (args.length == 1) {
            if (isNaN(new Date(a).valueOf())) {
                ft = a
            } else {
                dt = new Date(typeof a == 'string' && a.includes('-') ? a.replace(/-/g, '/') : a)
            }
        } else if (args.length >= 2) {
            dt = new Date(typeof a == 'string' && a.includes('-') ? a.replace(/-/g, '/') : a)
            ft = b
        }
        const map = {
            Y: String(dt.getFullYear()), //4位数 年份
            M: String(dt.getMonth() + 1).padStart(2, 0), // 2位数 月份
            D: String(dt.getDate()).padStart(2, 0), // 2位数 日期
            H: String(dt.getHours()).padStart(2, 0), // 2位数 时
            m: String(dt.getMinutes()).padStart(2, 0), // 2位数 分
            s: String(dt.getSeconds()).padStart(2, 0), //2位数 秒
            S: String(dt.getMilliseconds()).padStart(3, 0), // 3位数 毫秒
            Q: Math.floor((dt.getMonth() + 3) / 3) + '' //季度
        }
        return ft.replace(/\[([^\]]+)]|y{1,4}|Y{1,4}|M{1,2}|d{1,2}|D{1,2}|h{1,2}|H{1,2}|m{1,2}|s{1,2}|q|t|S{1,3}/g, match => {
            // 匹配中的首字符
            let k = match[0]
            // 匹配到的字符串长度
            let len = match.length
            switch (k) {
                case 'y':
                case 'Y':
                    return match.replace(new RegExp('((' + k + ')+)', 'g'), a => map.Y.substr(4 - a.length))
                case 'M':
                    return len == 1 ? Number(map.M) : map.M
                case 'D':
                case 'd':
                    return len == 1 ? Number(map.D) : map.D
                case 'H':
                case 'h':
                    return len == 1 ? Number(map.H) : map.H
                case 'm':
                    return len == 1 ? Number(map.m) : map.m
                case 's':
                    return len == 1 ? Number(map.s) : map.s
                case 'S':
                    return match.replace(new RegExp('((' + k + ')+)', 'g'), a => map.S.substr(3 - a.length))
                case '[':
                    return match.replace(/\[|\]/g, '')
                case 'q':
                    return map.Q
                default:
                    return match
            }
        })
    } catch (e) {
        return new Date('') // Invalid Date
    }
}
```

实现完了，到底具有那些能力，我们测试下，大致罗列如下：

```js
// 使用默认格式格式化当前日期
format()

// 指定格式来格式化当前日期
format('yyyy-MM-dd')

// 使用默认格式来格式化指定日期
format('2021/1/1')
// => "2021-01-01 00:00:00"

// 指定格式来格式化指定日期
format('2021/1/1', 'yy-MM-dd hh:mm:ss S')
// => "21-01-01 00:00:00 000"

// 模板处理特殊字符
format('2021/1/1', 'yyyy-MM-dd [yyyy]')
// => "2021-01-01 yyyy"

format('2021/1/1', '2021/1/1是属于第q季度')
// => "2021/1/1是属于第1季度"

format('当前时间是属于第q季度')
```

所有可用解析标记的列表，如果又其他需求，可自行扩展即可

|标识        | 示例      | 描述            |
| --------- | ------- | ---------------- |
| YY/yy     | 18      | 年，两位数        |
| YYYY/yyyy | 2018    | 年，四位数        |
| M         | 1-12    | 月，从 1 开始     |
| MM        | 01-12   | 月，两位数字      |
| D/d       | 1-31    | 日               |
| DD/dd     | 01-31   | 日，两位数        |
| H/h       | 0-23    | 24 小时           |
| HH/hh     | 00-23   | 24 小时，两位数    |
| h         | 1-12    | 12 小时           |
| hh        | 01-12   | 12 小时，两位数    |
| m         | 0-59    | 分钟              |
| mm        | 00-59   | 分钟，两位数       |
| s         | 0-59    | 秒                |
| ss        | 00-59   | 秒，两位数         |
| S         | 0-9     | 毫秒（百），一位数 |
| SS        | 00-99   | 毫秒（十），两位数 |
| SSS       | 000-999 | 毫秒，三位数       |
| q         | 季度    | 返回 1 ~ 4         |