# Class类
JS中的类，本质是上一个对象

类里面有构造器 constructor

类中你所定义的代码，会自动帮你开启严格模式，所以默认的 this 会指向 undefined

还可以自定义方法，这种自定义方法叫做一般方法，例如下方代码中的 speak()

如果需要使用某个方法的话，在原型上没有，就会去原型链上面找

如果子类继承了父类，且子类中写了构造器，那么子类构造器中的super方法是必须调用的

super 方法会帮助你把子类获得的参数传给父类

super 方法需要写在构造器的最前面

extends 是继承的意思，详细用法可以看下面的 Student 继承于 Person

``` javascript 
 // 创建一个Persion类，创建一个Persion的示例对象
 class Person {
 
	 // 构造器方法↓
	 constructor (name, age){
		// 构造器中的this是类的示例对象
		this.name = name
		this.age = age
	 }	

	 // 一般方法
	 speak() {
	 
		 // speak 方法放在了哪里 - 类的原型对象上
		 // speak 方法给谁用 - 供实例使用
		 // 通过 Person 实例调用 speak 时，speak 中的 this 就是 Person 实例
		 console.log(`我叫${this.name},我的年龄是${this.age}`)
	 }
 }

 // const p1 = new Person('Tom', 18)
 // const p2 = new Person('Jerry', 20)

 // console.log(p1)
 // Persion => 类名    {} => p1
 // console.log(p2)
 // Persion => 类名    {} => p2

 // p1.speak()
 // p2.speak()
 // 调用原型中的speak方法
 

 // 创建一个 Student 类，继承于 Person 类
 class Student extends Person {
 
	 constructor (name, age, grade){
		 // 构造器中的this是类的示例对象
		 // super 函数必须放在所有人之前 super 方法是调用父类
		 super(name, age)
		 this.grade = grade
	 }

 	// 重写从父类继承来的方法
	 speak() {
	 	console.log(`我叫${this.name},我的年龄是${this.age},我的读的是${this.grade}`)
	 }
	 
	 // 子类自己的方法
	 study() {
	 	console.log('我在很努力的学习')
	 }
 }

 // const s1 = new Student('小张',15,'高一')
 // console.log(s1)
 // s1.speak()
 // 因为方法重写过后，所以调用的是 Student类 中的 speak 方法
 // s1.study()
 // 调用 Student 类中的 study 方法
 
 
 /*
	 总结：
		 1. 类中的构造器不是必须要写的，要对实例进行一些初始化的操作，如添加指定属性时才写
		 2. 如果A类继承了B类，且A类中写了构造器，那么A类构造器中的super是必须调用的
		 3. 类中定义的方法都是放在类的原型对象上的
 */
```
class 类指向问题
```javascript
 class Person {
	 constructor(name,age) {
		 this.name = name
		 this.age = age
	 }
	 speak(){
	 	console.log(this)
	 }
 }
 
 const p1 = new Person('tom',18)
 
 // 实例调用,所以里面的this会向原型找去,找到speak方法
 p1.speak()
 
 // class会默认生成严格模式
 // 赋值的话,就相当于直接调用,多了一个引用地址
 // 所以找到的this为undefined
 const x = p1.speak
 x() // undefined
```
类中可以直接写赋值语句,如下代码的含义是给 car 的实例对象添加一个属性，名为 a ，值为 1
```javascript
class Car {
 	constructor(name,price){
 		this.name = name
 		this.price = price
	}
	// 类中可以直接写赋值语句,如下代码的含义是给 car 的实例对象添加一个属性，名为 a ，值为 1
	a = 1
}
 const c1 = new Car('奔驰',199)
 console.log(c1) // Car {a: 1, name: "奔驰", price: 199}
```