---
title: 函数
date: 2023-03-08 22:16:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 函数

### 箭头函数

箭头函数无法使用super、arguments和new.target，不能作为构造函数，同时也没有prototype属性

### 默认参数

参数按照定义初始化，后定义默认值的参数可以引用先定义的参数

默认参数存在自己的声明式参数作用域

```javascript
function f1(x = 1, y = () => x = 10) {
    y()
    console.log(x) // 10
    x = '22'
}
function f2(x = 1, y = () => x = 10) {
    y()
    console.log(x) // 1
    var x = '22'
}

```

![默认参数作用域](default-param.png)

### 判断函数是否使用new关键字调用

```javascript
function King() {
    if (!new.target) {
        throw 'must be instantiated using new'
    }
}
new King()
King() // Error: King must be instantiated using new
```

### 函数属性和方法

每个函数都存在两个属性：length和prototype。length属性保存函数定义的命名参数的个数

call()向函数传参时，必须将参数一个个列出来

apply()向函数传参时，可以传递一个参数数组

bind()会创建一个新的函数实例，其this值会被绑定到传给bind的对象

```javascript
function sayName(name) {
    console.log(name)
}
console.log(sayName.length) // 1
function getArray(num1, num2) {
    return Array.of.apply(this, arguments)
}
function getArray2(num1, num2) {
    return Array.of.call(this, num1, num2)
}
var color = 'red'
var obj = {
    color: 'blue'
}
function sayColor() {
    console.log(this.color)
}
let newSayColor = sayColor.bind(o)
newSayColor() // blue
```

### 函数声明与函数表达式

函数声明会在代码执行前，进行定义

```javascript
// 这种方式是错误的
if (condition) {
    function sayHi() {
        cosole.log('hi')
    }
} else {
    function sayHi() {
        console.log('yo')
    }
}
```

### 尾调用优化

- 代码在严格模式下运行
- 外部函数的返回值是对尾调用函数的调用
- 尾调用函数返回后不需要执行额外的逻辑
- 尾调用函数不是引用外部函数作用域中自由变量的闭包

### 闭包

```javascript
function createComparisonFunction(propertyName) {
    return function(obj1, obj2) {
        let value1 = obj1[propertyName]
        let value2 = obj2[propertyName]
        if (value1 < value2) {
            return -1
        } else if (value1 > value2) {
            return 1
        } else {
            return 0
        }
    }
}
```

在执行createComparisonFunction时，会在内部的作用域链上创建两个对象，一个是指向内部的活动对象，一个是指向外部的全局对象，因为返回的匿名函数引用了createComparisonFunction的活动对象，导致在createComparisonFunction执行完后，活动对象并不能直接销毁

![作用域链](scope.png)

### 私有变量

```javascript
class Test {
    constructor(name) {
        this._name = name
    }
    getName() {
        return this._name
    }
}
const ProxyTest = new Proxy(Test, {
    construct(target, args, newTarget) {
        const instance = Reflect.construct(...arguments)
        return new Proxy(instance, {
            get(target, prop, receiver) {
                if (prop[0] === '_') {
                    throw 'can not access private field'
                }
                const val = Reflect.get(...arguments)
                return typeof val === 'function' ? val.bind(target) : val
            }
        })
    }
})

export default ProxyTest
```

