---
title: 对象、类和面向对象编程
date: 2023-02-26 12:08:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 第八章 对象、类和面向对象编程

### 对象

#### 对象的属性

##### 数据属性

```javascript
let person = {};
Object.defineProperty(person, "name", {
    configurable: true, // 是否可以删除属性，可以修改它的特性
    enumerable: true, // 是否可以通过for-in循环
    writable: true, // 是否可以修改属性值
    value: "test" // 属性实际的值
})
```

##### 访问器属性

```javascript
let person = {};
Object.defineProperty(person, "name", {
    configurable: true, // 是否可以删除属性，可以修改它的特性
    enumerable: true, // 是否可以通过for-in循环
    get() {
        return "test";
    },
    set(newValue) {
        xxx
    }
})
```

##### Object.defineProperty

- 如果configurable、enumerable和writable不指定则默认为false

- set属性如果未定义，则默认是只读的

##### Object.getOwnPropertyDescriptor

```javascript
let descriptor = Object.getOwnPropertyDescriptor(person, "name");
console.log(descriptor);
/* descriptor = {
	configurable: true,
	enumerable: true,
	get: function(){},
	set: undefined
} */
```

##### Object.assign

将多个对象的属性进行合并，对于同名的属性，取最后一个值；对于访问器属性，比如获取函数，会作为一个静态值赋给对象

```javascript
let dest, src, result;
dest = { id: 'dest' };
result = Object.assign(dest, { id: 'first' }, { id: 'second' });
console.log(result); // { id: 'second' }
```

##### 计算属性

中括号包围的对象属性键，在运行时会作为JavaScript表达式运行，而不是作为字符串

```javascript
const name = 'test';
let person = {
    [name]: '111',
    [methodKey](name) {
        console.log(name);
    }
}
```

##### 解构

解构在内部使用函数ToObject()将源数据结构转换为对象，这意味着在对象解构的上下文中，原始值会被当作对象处理。这也意味着null、undefined不能被解构，否则会报错

```javascript
let { length } = "test";
console.log(length) // 4
let { _ } = null // TypeError
let { _ } = undefined // TypeError
```

对于之前声明的变量赋值，赋值表达式必须包含在一对括号中

```javascript
let personAge;
({ age: personAge } = { age: 10});
console.log(personAge) // 10
let age;
({ age } = { age: 100 });
console.log(age) // 100
```

### 继承

![原型链](jsobj_full.jpg)

#### 原型继承

##### 实现

```javascript
function SuperType() {
    this.property = true;
}
SuperType.prototype.getSuperValue = function() {
    return this.property;
}
function SubType() {
    this.subproperty = false;
}
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function() {
    return this.subproperty;
}
let instance = new SubType();
console.log(instance.getSuperValue()) // true
```

##### 缺点

- 实例属性最终变成了原型属性，被所有实例共享

- 子类在实例化时，不能给父类的构造函数传参

#### 盗用构造函数

##### 实现

```javascript
function SuperType() {
    this.colors = ['red', 'blue', 'green']
}
function SubType() {
    SuperType.call(this);
}
```

##### 缺点

- 无法访问父类原型上的方法，只能将方法定义在构造函数中

#### 组合继承

##### 实现

```javascript
function SuperType() {
    this.colors = ['red', 'blue', 'green']
}
SuperType.prototype.getName = function() {
    console.log(this.colors)
}
function SubType(age) {
    // 继承属性
    SuperType.call(this)
    this.age = age;
}
// 继承方法
SubType.prototype = new SuperType()
SubType.prototype.getAge = function() {
    console.log(this.age)
}

```

##### 缺点

- 父类的构造方法需要初始化两次

#### 原型式继承

##### 实现

```javascript
function object(o) {
    function F() {}
    F.prototype = o
    return new F()
}
// 或者使用es6新增的Object.create()
let other = Object.create(o)
```

#### 寄生式继承

##### 实现

```javascript
function createAnother(o) {
    let clone = Object.create(o)
    clone.sayHi = function() {
        console.log('hi')
    }
    return clone
}
```

##### 缺点

- 每个对象上都要定义一次方法，不能复用

#### 寄生式组合继承

##### 实现

```javascript
function inheritPrototype(subType, superType) {
    let prototype = Object.create(superType)
    prototype.constructor = subType
    subType.prototype = prototype
}
function SuperType() {
    this.colors = ['red', 'blue', 'green']
}
SuperType.prototype.getName = function() {
    console.log(this.colors)
}
function SubType(age) {
    // 继承属性
    SuperType.call(this)
    this.age = age;
}
// 继承方法
inheritPrototype(SubType, SuperType)
SubType.prototype.getAge = function() {
    console.log(this.age)
}

```

### 类

#### 类定义

##### 定义

```javascript
// 类声明
class Person {}
// 类表达式
const Animal = class {}
// 把类表达式赋值给变量后，不能在类表达式作用域外部访问这个标识符
let Person = class PersonName {
}
let p = new Person()
console.log(Person.name) // PersonName
console.log(PersonName) // PersonName is not defined
```

##### 特点

- 类声明无法被提升
- 类受块作用域限制

#### 类构造函数

##### 实例化

- 在内存创建一个新对象
- 对象内部的[[Prototype]]指针被赋值给构造函数的prototype属性
- 构造函数内部的this被赋值为这个新对象
- 执行构造函数的代码
- 如果构造函数返回非空对象，则返回该对象，否则，返回刚才新建的对象

#### 继承

##### 定义

```javascript
class Vehicle {}
class Bus extends Vehicle {
    constructor() {
        super()
    }
}
```

##### 特点

- super只能在构造方法和静态方法中使用
- 不能单独引用super关键字，console.log(super)会抛出错误
- 没有定义构造函数时，在实例化子类会调用super()，并且传入所有传给子类的参数
- 类构造函数中，不能在调用super()之前调用this
- 子类中显示定义了构造函数，则必须在其中调用super()，或者返回一个对象

##### 类混入

extends后面是一个JavaScript表达式，任何可以解析为一个类或者构造函数的表达式都是有效的

```javascript
class Vehicle {}
let FooMixin = (Superclass) => class extends Superclass {
    foo() {}
}
let BarMixin = (Superclass) => class extends Superclass {
    bar() {}
}
class Bus extends FooMixin(BarMixin(Vehicle)) {

}
```

### 参考链接

[1] <http://www.mollypages.org/tutorials/js.mp>
