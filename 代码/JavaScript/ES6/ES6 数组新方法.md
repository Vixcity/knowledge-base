# Array.from() 
## Array.from 方法在于把一个类数组对象转成一个真正的数组可遍历对象
```js
const todos = document.querySelectorAll('li')
const names = todos.map(todo => todo.content)
console.log(names)
```
此时会报错
>Uncaught TypeError: todos.map is not a function at \<anonymous>:2:21

因为 todos 并不是一个真正的数组，他是一个类数组对象
	
为了能调用这个 map 方法，我们现在需要把 todos 类数组转换成为真正的数组

```js
const todos = document.querySelectorAll('li')
const todosArr = Array.from(todos)
const names = todosArr.map(todo => todo.content)
console.log(names)
```
	
Array.from 还可以传入第二个参数，第二个参数相当于数组的 map 方法
	
第二个参数是一个函数类型的

```js
const todos = document.querySelectorAll('li')
const names = Array.from(todos,todo => todo.content)
console.log(names)
```

## Array.from 方法还可以把函数的参数转化成数组对象
```js
function sum(){
	// 不用Array方法也可以用for of 方法来遍历
	return arguments.reduce((prev,cuur) => prev+cuur,0)
}
```

他会找错，因为参数是一个类数组
> VM329:2 Uncaught TypeError: arguments.reduce is not a function at sum (\<anonymous>:2:19) at \<anonymous>:1:1

```js
function sum(){
	return Array.from(arguments).reduce((prev,cuur) => prev+cuur,0)
}
```

## 可以把可迭代对象转成数组（类似str等）

```js
const websit = 'Vixcity'
Array.from(websit)
// (7) ["V", "i", "x", "c", "i", "t", "y"]
```

# Arrray.of()
该函数的出现是为了弥补 array 这个构造函数的不足

new Array 会根据传入参数的不同，返回不同长度的数组

Array.of() 不管你传入多少个参数，他都会返回传入的参数组成的数组

```js
Array.of(1,2,3,5,4,564,6,8,4,84,31)
// (11) [1, 2, 3, 5, 4, 564, 6, 8, 4, 84, 31]
```

保证返回数组的一致性

# .find()
寻找数组当中的某个符合要求的元素，立马返回符合要求的元素
```js
const frults = [
	{name:'apple',money:2},
	{name:'banana',money:3},
	{name:'orange',money:9}
]

const banana = frults.find((value,index,frults) => {
    console.log(value,index,frult)
	// {name: "apple", money: 2} 0 (3) [{…}, {…}, {…}]
	// {name: "banana", money: 3} 1 (3) [{…}, {…}, {…}]
    if(value.name === 'banana'){
        return true
    }
    return false
})

console.log(banana)
// {name: "banana", money: 3}
```

简写

```js
const banana = frults.find(frult => frult.name === 'banana')
```

# .findIndex()
用法与上面的 find 相同
```js
const frults = [
	{name:'apple',money:2},
	{name:'banana',money:3},
	{name:'orange',money:9}
]

const banana = frults.findIndex((value,index,frults) => {
    console.log(value,index,frult)
	// {name: "apple", money: 2} 0 (3) [{…}, {…}, {…}]
	// {name: "banana", money: 3} 1 (3) [{…}, {…}, {…}]
    if(value.name === 'banana'){
        return true
    }
    return false
})

console.log(banana)
// 1
```

简写

```js
const banana = frults.findIndex(frult => frult.name === 'banana')
// 1
```

# .some()
传入的值与上面相同，某个元素满足测试函数返回 true 
```js
const frults = [
	{name:'apple',money:2},
	{name:'banana',money:3},
	{name:'orange',money:9}
]

const banana = frults.some(frult => frult.money > 2)
// true
```
>PS：find为返回当前元素，这两个不一样

# .every()
所有的元素都满足才会通过，如果有一个是false的话，那么就会立即返回
```js
const frults = [
	{name:'apple',money:2},
	{name:'banana',money:3},
	{name:'orange',money:9}
]

const banana = frults.ecery(frult => frult.money > 3)
// false
```