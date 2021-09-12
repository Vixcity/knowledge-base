## 图解 JavaScript 继承，一文搞定继承相关知识

继承是面向对象编程中讨论最多的话题

很多面向对象语言都支持两种继承：接口继承和实现继承

- 前者只继承方法签名
- 后者继承实际的方法

接口继承在 ECMAScript 中是不可能的，因为函数没有签名

实现继承是 ECMAScript 唯一支持的继承方式，而这主要是通过原型链实现的

## 原型知识前置

```js
var Person = function(name){  
  this.name = name; // tip: 当函数执行时这个 this 指的是谁？  
};  
Person.prototype.getName = function(){  
  return this.name;  // tip: 当函数执行时这个 this 指的是谁？  
}  
var person1 = new Person('Mick');  
```

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122052736.webp)
## 继承
### 原型链继承

原型链定义为ECMA的主要继承方式

```js
function Parent () {  
    this.name = 'kevin';  
}  
  
Parent.prototype.getName = function () {  
    console.log(this.name);  
}  
  
function Child () {

}  
  
Child.prototype = new Parent();  
var child1 = new Child();  
console.log(child1.getName())
```

  
原先是这样：  

![原先](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122054680.webp)

现在是这样：  

![现在](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122054891.webp)

Child创建的对象的__proto__指向new Parent对象。contructor指向Parent构造函数。这里有一个问题是没有将原型对象的contructor指向Child。加一行代码就可以了。

```js
Child.prototype.constructor = Child;  
```

#### 缺点

-   引用类型的属性被所有实例共享，**值类型不会被共享，是因为当我们在执行\[[get]]操作的时候会去查询原型链。执行\[[put]]的时候，会自动给当前的对象创建一个属性**。
-   在创建 Child 的实例时，不能向Parent传参
```js
function Parent () {  
    this.name = 'kevin';  
   this.information = {  
     age: 12  
    }  
}  
  
Parent.prototype.getName = function () {  
    console.log(this.name);  
}  
  
function Child () {  
  
}  
  
Child.prototype = new Parent();  
  
var child1 = new Child();  
var child2 = new Child();  
  
console.log(child1.getName())  
```

子类型在实例化时不能给父类型的构造函数传参

事实上，我们无法在不影响所有对象实例的情况下把参数传进父类的构造函数

再加上之前提到的原型中包含引用值的问题，就导致原型链基本不会被单独使用  
### 借用构造函数继承【经典继承】

为了解决**原型包含引用值导致的继承问题**，一种叫作“盗用构造函数”（constructor stealing）的技术在开发社区流行起来（这种技术有时也称作“**对象伪装**”或“**经典继承**”）

基本思路很简单：在子类构造函数中调用父类构造函数

因为毕竟函数就是在特定上下文中执行代码的简单对象，所以可以使用apply()和 call()方法以新创建的对象为上下文执行构造函数。  

```js
function Parent (name) {  
    this.name = name;  
}  
  
function Child (name) {  
    Parent.call(this, name);  
}  
  
var child1 = new Child('kevin');  
  
console.log(child1.name); // kevin  
  
var child2 = new Child('daisy');  
  
console.log(child2.name); // daisy  
```
  
原先是这样：  

![原先](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122100057.webp)

现在是这样：  

![现在](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122100535.webp)

从上图可以清楚看到：  
当前的继承方式是通过new操作的时候，绑定child的this执行Parent的方法，去执行一次Parent内部的方法，和原型链啥的没有任何关系。

#### 缺点

盗用构造函数的主要缺点，也是使用构造函数模式自定义类型的问题：必须在构造函数中定义方法，因此**函数不能重用【因为二者之间的函数是两个函数，本质是不相等的函数】**

此外，子类也不能访问父类原型上定义的方法，因此所有类型只能使用**构造函数模式**

由于存在这些问题，盗用构造函数基本上也不能单独使用。  

### 组合继承

组合继承（有时候也叫伪经典继承）综合了原型链和盗用构造函数，将两者的优点集中了起来。

```js
function Parent (name) {  
  console.log('执行Parent')  
    this.name = name;  
    this.colors = ['red', 'blue', 'green'];  
    this.publicMethod = () => {  
  }  
}  
  
Parent.prototype.getName = function () {  
    console.log(this.name)  
}  
  
function Child (name, age) {  
    Parent.call(this, name);  
    this.age = age;  
}  
  
Child.prototype = new Parent();  
Child.prototype.constructor = Child;  
  
var child1 = new Child('kevin', '18');  
  
child1.colors.push('black');  
  
console.log(child1.name); // kevin  
console.log(child1.age); // 18  
console.log(child1.colors); // ["red", "blue", "green", "black"]  
  
var child2 = new Child('daisy', '20');  
  
console.log(child2.name); // daisy  
console.log(child2.age); // 20  
console.log(child2.colors); // ["red", "blue", "green"]  
```

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122101320.webp)

组合继承弥补了原型链和盗用构造函数的不足，是 JavaScript 中使用最多的继承模式

而且组合继承也保留了 instanceof 操作符和 isPrototypeOf()方法识别合成对象的能力  

### 原型式继承

2006 年，Douglas Crockford 写了一篇文章：《JavaScript 中的原型式继承》（“Prototypal Inheritance in JavaScript”）

这篇文章介绍了一种不涉及严格意义上构造函数的继承方法

他的出发点是即使不自定义类型也可以通过原型实现对象之间的信息共享

```js
function object(o) {   
 function F() {}   
 F.prototype = o;   
 return new F();   
}  
  
let person = {   
 name: "Nicholas",   
 friends: ["Shelby", "Court", "Van"]   
};   
  
let anotherPerson = object(person);   
anotherPerson.name = "Greg";   
anotherPerson.friends.push("Rob");   
  
let yetAnotherPerson = object(person);   
yetAnotherPerson.name = "Linda";   
yetAnotherPerson.friends.push("Barbie");   
console.log(person.friends); // "Shelby,Court,Van,Rob,Barbie"  
```

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122102827.webp)
  
原型式继承虽然说是不自定义类型来实现原型对象之间的信息共享，但是我们可以发现object里面还是自定义了一个类型，去实现原型链继承的方式。  
  
**特定场景**：原型式继承非常适合不需要单独创建构造函数，你有一个对象，想在它的基础上再创建一个新对象。  
  
ECMAScript 5 通过增加 **Object.create()** 方法将原型式继承的概念规范化了

这个方法接收两个参数：作为新对象原型的对象，以及给新对象定义额外属性的对象（第二个可选）。  

### 寄生式继承

与原型式继承比较接近的一种继承方式是寄生式继承（parasitic inheritance），也是 Crockford 首倡的一种模式

寄生式继承背后的思路类似于寄生构造函数和工厂模式：创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象。  
  
这种模式依赖于上面的object方法，这里直接使用Obect.create()方法

```js
function createObj (o) {  
    var clone = Object.create(o);  
    clone.sayName = function () {  
        console.log('hi');  
    }  
    return clone;  
}  
```

这里的图和上面的基本相同，就不画了。这里说的是一种思想，在createObj的方法里统一对我们创建的对象做一些处理。  

### 寄生组合式继承

组合继承其实也存在效率问题

最主要的效率问题就是父类构造函数始终会被调用两次：一次在是创建子类原型时调用，另一次是在子类构造函数中调用

本质上，子类原型最终是要包含超类对象的所有实例属性，子类构造函数只要在执行时重写自己的原型就行了。  
  
可以翻到组合继承的地方，看到Parent会被调用两次，call一次，原型对象一次。  

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122103601.webp)

寄生式组合继承通过盗用构造函数继承属性，但使用混合式原型链继承方法

基本思路是**不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本**

说到底就是使用寄生式继承来继承父类原型，然后将返回的新对象赋值给子类原型。  

```js
function inheritPrototype(subType, superType) {  
    let prototype = Object.create(superType.prototype); // 创建对象  
    prototype.constructor = subType; // 增强对象  
    subType.prototype = prototype; // 赋值对象  
}  
  
function SuperType(name) {  
    this.name = name;  
    this.colors = ["red", "blue", "green"];  
}  
  
SuperType.prototype.sayName = function() {  
    console.log(this.name);  
};  
  
function SubType(name, age) {  
   console.log('执行SubType')  
    SuperType.call(this, name);  
    this.age = age;  
}  
  
inheritPrototype(SubType, SuperType);  
SubType.prototype.sayAge = function() {  
    console.log(this.age);  
};  
  
var subTupe1 = new SubType('name1', 1)  
var subTupe2 = new SubType('name2', 2)  
```

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122104599.webp)
## 总结继承

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122104964.webp)

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122104022.webp)

## 思考问题：

每个构造函数都有一个原型对象，原型有一个属性指回构造函数，而实例有一个内部指针指向原型

如果原型是另一个类型的实例呢？

那就意味着这个原型本身有一个内部指针指向另一个原型，相应地另一个原型也有一个指针指向另一个构造函数

这样就在实例和原型之间构造了一条原型链。

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122105986.webp)

### 为什么要有继承呢？

通过【某种方式】让一个对象可以访问到另一个对象中的属性和方法，我们把这种方式称之为继承。避免在某一个构造函数中声明创建过多的方法。造成内存增加。  

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202109122105136.webp)

### 为什么会有借用构造函数继承呢？

首先谈到的是原型继承的缺点：  
  
继承的本质还是想共享属性，方法，实例当中有着自己从原型当中继承而来的属性和方法，**自己独有**

但是现在对于**引用属性是共享**的，实例之间彼此会互相影响

而且对于构造函数当中的属性值，不能通过传入参数来进行定义。  
  
构造函数是为了解决这个问题的。  
  
但是构造函数也有自己的缺点。没有应用原型链，失去了原型链的意义。

实际上是普通函数的借用而已。所以基本不会单独使用。  

### 组合继承的来源

-   是将原型链继承和借用构造函数继承。
-   原型继承实现原型链，借用构造函数继承，实现创建实例属性。
    
### 原型式继承和寄生式继承来源

解决原型继承需要调用父类构造函数创建原型对象指向，通过原型式和寄生式来实现。  

### 寄生组合方式

则是在组合方式下，通过寄生继承来实现原型链指向问题。