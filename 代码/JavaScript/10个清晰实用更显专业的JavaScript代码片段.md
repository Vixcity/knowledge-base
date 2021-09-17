## 10个清晰实用更显专业的JavaScript代码片段

这是一些非常酷，非常有用的速记代码片段

使用这些代码片段，可以使你的代码看起来更加的专业

### **1、单行If-Else语句(三元表达式)**

我们最常用的可能是这样的if-else语句：
```js
if (10 < 100) {
	console.log("true");
} else {
	console.log("false");
}  
```
输出
```js
true  
```
我们可以通过使用三元表达式，用更简洁的方式来重新编写上面的代码
```js
10 <100 ？console.log（“true”）：console.log（“false”）  
```
输出：
```js
true  
```
通常，三元运算符遵循以下简单模式：
```js
条件 ? 为真时候的表达式 : 为假时候的表达式
```
三元运算符也可以链接在一起以形成更长的链。但是，它通常会使代码变得很冗长。明智地使用它们，不会使事情变得更复杂。

### **2、合并数组(扩展运算符)**

我们在平常使用中可能需要合并两个数组

那么这个时候我们可以用扩展运算符（...）

将一个数组的元素扩展为另一个数组，例如：

```js
const numbers = [10, 20, 30, 40];
const allNumbers = [...numbers, 50, 60, 70, 80];
console.log(allNumbers);  
```

输出：

```js
[10, 20, 30, 40, 50, 60, 70, 80]  
```

也可以用concat方法

例如：
```js
const numbers = [10, 20, 30, 40];
const numbersa=[1,2,3,4,5,6]
const newArr = numbers.concat(numbersa)
console.log(a)
```

输出：
```js
[10, 20, 30, 40, 1, 2, 3, 4, 5, 6]
```

### **3、从数组中删除重复项(数组去重)**
#### ES6 Set 去重
 不考虑兼容性，这种去重的方法代码最少

这种方法还无法去掉“{}”空对象

后面的高阶方法会添加去掉重复“{}”的方法

这个不会更改与原数组的数据
```js
const numbers = [1, 1, 20, 3, 3, 3, 9, 9];
const uniqueNumbers = [...new Set(numbers)];
console.log(uniqueNumbers);  
```

输出：

```js
[1, 20, 3, 9]  
```
#### 利用for嵌套for，然后splice去重
```js
function unique(arr){            
	for(var i=0; i<arr.length; i++){
		for(var j=i+1; j<arr.length; j++){
			if(arr[i]==arr[j]){         //第一个等同于第二个，splice方法删除第二个
				arr.splice(j,1);
				j--;
			}
		}
	}
	return arr;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", 15, false, undefined, NaN, NaN, "NaN", "a", {…}, {…}]     
//NaN和{}没有去重，两个null直接消失了
```
双层循环，外层循环元素，内层循环时比较值

值相同时，则删去这个值

这个是会更改与原数组的数据的
#### 利用indexOf去重
```js
function unique(arr) {
	// 判断是不是数组，不是的话直接返回
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    var array = [];
    for (var i = 0; i < arr.length; i++) {
        if (array .indexOf(arr[i]) === -1) {
            array .push(arr[i])
        }
    }
    return array;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
// [1, "true", true, 15, false, undefined, null, NaN, NaN, "NaN", 0, "a", {…}, {…}]  
//NaN、{}没有去重
```

新建一个空的结果数组，for 循环原数组

判断结果数组是否存在当前元素，如果有相同的值则跳过

不相同则push进数组

这个不会更改与原数组的数据
#### 利用sort()
```js
function unique(arr) {
	// 判断是不是数组，不是的话直接返回
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return;
    }
	// 排序
    arr = arr.sort()
    var arrry= [arr[0]];
    for (var i = 1; i < arr.length; i++) {
        if (arr[i] !== arr[i-1]) {
            arrry.push(arr[i]);
        }
    }
    return arrry;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
// [0, 1, 15, "NaN", NaN, NaN, {…}, {…}, "a", false, null, true, "true", undefined]      
//NaN、{}没有去重
```
利用sort()排序方法

然后根据排序后的结果进行遍历及相邻元素比对

不相同则push进新数组

这个不会更改与原数组的数据，但是会改变原数组排序
#### 利用对象的属性不能相同的特点进行去重（这种数组去重的方法有问题，不建议用，有待改进）
```js
function unique(arr) {
	// 判断是不是数组，不是的话直接返回
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    var arrry= [];
	var  obj = {};
    for (var i = 0; i < arr.length; i++) {
        if (!obj[arr[i]]) {
            arrry.push(arr[i])
            obj[arr[i]] = 1
        } else {
            obj[arr[i]]++
        }
    }
    return arrry;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", 15, false, undefined, null, NaN, 0, "a", {…}]    
//两个true直接去掉了，NaN和{}去重
```
#### 利用includes

```js
function unique(arr) {
	// 判断是不是数组，不是的话直接返回
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    var array =[];
    for(var i = 0; i < arr.length; i++) {
            if( !array.includes( arr[i]) ) {//includes 检测数组是否有某个值
                    array.push(arr[i]);
              }
    }
    return array
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]  //{}没有去重
```
检测数组是否有某个值

没有则push进新数组

这个不会更改与原数组的数据
#### 利用hasOwnProperty
```js
function unique(arr) {
    var obj = {};
    return arr.filter(function(item, index, arr){
        return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
    })
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}]
//所有的都去重了
```
利用hasOwnProperty 判断是否存在对象属性

#### 利用filter
```js
function unique(arr) {
  return arr.filter(function(item, index, arr) {
    //当前元素，在原始数组中的第一个索引==当前索引值，否则返回当前元素
    return arr.indexOf(item, 0) === index;
  });
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", true, 15, false, undefined, null, "NaN", 0, "a", {…}, {…}]
```
#### 利用递归去重
```js
function unique(arr) {
	var array = arr;
	var len = array.length;

    array.sort(function(a,b){   //排序后更加方便去重
        return a - b;
    })

    function loop(index){
        if(index >= 1){
            if(array[index] === array[index-1]){
                array.splice(index,1);
            }
            loop(index - 1);    //递归loop，然后数组去重
        }
    }
    loop(len-1);
    return array;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "a", "true", true, 15, false, 1, {…}, null, NaN, NaN, "NaN", 0, "a", {…}, undefined]
```

#### 利用Map数据结构去重
```js
function arrayNonRepeatfy(arr) {
	let map = new Map();
	let array = new Array();  // 数组用于返回结果
	for (let i = 0; i < arr.length; i++) {
		if(map .has(arr[i])) {  // 如果有该key值
	  		map .set(arr[i], true); 
		} else { 
			map .set(arr[i], false);   // 如果没有该key值
			array .push(arr[i]);
		}
	} 
	return array ;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
    console.log(unique(arr))
//[1, "a", "true", true, 15, false, 1, {…}, null, NaN, NaN, "NaN", 0, "a", {…}, undefined]
```
创建一个空Map数据结构，遍历需要去重的数组

把数组的每一个元素作为key存到Map中

由于Map中不会出现相同的key值

所以最终得到的就是去重后的结果
### 利用reduce+includes
```js
function unique(arr){
    return arr.reduce((prev,cur) => prev.includes(cur) ? prev : [...prev,cur],[]);
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

### **4、将任何内容转换为布尔值**

除了true和false之外，JavaScript还将其他类型视为真或假。

-   0，""，null，undefined，NaN，和false总是假 。
-   其他一切都是真实的。
    

正因为如此，在JavaScript中，你可以将任何值转换为true和false与一元运算符（!）：

```js
const bool1 = !0;
// trueconst bool2 = !100;
// falseconst bool3 = !"test";
// falseconst bool4 = !!"test";
// trueconsole.log(bool1, bool2, bool3, bool4);  
```

输出：

```js
true false false true  
```

### **5、交换两个变量而没有第三个变量(解构赋值)**

```js
let x = 1;
let y = 2;
[x, y] = [y, x];
console.log(x, y);  
```

输出：

```js
2 1  
```

### **6、将数字转换为字符串**

```js
const num = 1 +“”;
console.log（typeof num）; 
console.log（num）;  
```

输出：

```js
string 1  
```

### **7、将字符串转换为数字**

```js
const numStr = "124";
const num = +numStr;console.log(typeof num);console.log(num);  
```

输出：

```js
number 84  
```

### **8、将变量嵌入到字符串(字符串模板)**

通过使用反引号（\`）将字符串括起来并将变量插入之间，将变量整齐地嵌入到字符串之间${}：

```js
const age = 41;const sentence = `I'm ${age} years old`;console.log(sentence);  
```

输出：

```js
I'm 41 years old  
```

### **9、将字符串拆分为数组(扩展运算符)**

要将字符串拆分为数组，可以使用扩展运算符（...）：

```js
const str = "Test"const strAsArr = [...str]console.log(strAsArr)  
```

输出：

```js
["T", "e", "s", "t"]  
```

### **10、可选链接**

> **_“可选的链接运算符（?.）允许读取位于连接对象链深处的属性的值，而不必明确验证链中的每个引用是否有效。” — MDN Web文档_**

假设你有一个data对象，并且想要安全地访问data.test.value

首先，你需要检查：

-   data 是否被定义。    
-   data.test 是否被定义。

在data声明之前，你不能调用data.test或者data.test.value，

否则会报错

因为，在JS中无法读取undefined身上的属性

因为undefined身上没有属性

也无法给undefined添加相对应的属性

```js
const data = {test：{value：1}}
if（data && data.test）{
	console.log（data.test.value）;
}  
```

输出：

```js
1  
````

幸运的是，使用可选链接的方法，你可以简单明了地编写上面的代码：

```js
const value = data?.test?.value;
console.log(value)  
```

输出：

```js
1  
```

现在，你还可以安全地尝试访问不存在的属性，而不会出现问题：

```js
console.log(data?.this?.does?.not?.exist?.for?.sure)  
```

输出：

```js
undefined  
```

### **加餐：提高JSON的可读性**

我确定你以前用过JSON.stringify()

但是，你可能不知道它的一种方法是可以对JSON数据格式进行整齐缩进的

让我们看看如何完成此任务。该JSON.stringify()方法接受两个可选参数：

-   替换功能，用于过滤显示的JSON。在这种情况下，可以是null，因为我们不需要它。
-   一个空格值，可以是所需空格的数目或字符串。在这种情况下，让我们使用制表符（'\t'）缩进JSON对象以使其看起来不错：
    
```js
const readableJSON = JSON.stringify({ a: 'A', b: 'B' }, null, '\t');
console.log(readableJSON);  
```

输出：

```js
{
	"a": "A",
	"b": "B"
} 
```
