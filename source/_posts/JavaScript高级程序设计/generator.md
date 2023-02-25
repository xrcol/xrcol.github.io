---
title: 迭代器与生成器
date: 2023-02-25 14:45:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 第七章 迭代器与生成器

### 迭代器

#### 基本使用方法

迭代器只是使用游标来记录遍历的对象，如果可迭代对象在迭代期间修改了，迭代器也会反映出对应的变化

```javascript
let arr = ['foo', 'bar']
let it = arr[Symbol.iterator]
console.log(it.next()) // { done: false, value: 'foo' }
arr.splice(1, 0, 'baz')
console.log(it.next()) // { done: false, value: 'baz' }
console.log(it.next()) // { done: false, value: 'bar' }
console.log(it.next()) // { done: true, value: undefined }
```

#### 自定义迭代器

实现Iterator接口的对象都可以作为迭代器使用

```javascript
class Counter {
    constructor(limit) {
        this.limit = limit
    }
    [Symbol.iterator]() {
        let count = 1
        let limit = this.limit
        return {
            next() {
                if (count <= limit) {
                    return { done: false, value: count++ }
                } else {
                    return { done: true, value: undefined }
                }
            }
        }
    }
}
let counter = new Counter(3)
for (let i of counter) {
    console.log(i) // 1, 2, 3
}
```

提前终止迭代器，自定义终止时需要执行的方法，调用return并不会强制迭代器进入关闭状态，即后面的遍历会接着上次的遍历结果

```javascript
class Counter {
    [Symbol.iterator]() {
        let count = 1
        let limit = this.limit
        return {
            next() {
                if (count <= limit) {
                    return { done: false, value: count++ }
                } else {
                    return { done: true, value: undefined }
                }
            },
            return() {
                console.log('exit early')
                return { done: true } // 必须返回有效的IteratorResult对象
            }
        }
    }
}
let counter = new Counter(3)
for (let i of counter) {
    if (i > 2) {
        break; // break,continue, return, throw提前退出都会触发
    }
    console.log(i)
} // 1, 2, exit early

// 解构操作未消费所有值时也会触发
let [a, b] = counter
```

### 生成器

#### 基本使用方法

在函数名称前加*号，来表示它是一个生成器，生成器也实现了Iterator接口，可以进行for...of迭代

```javascript
function* generator() {}
let foo = {
    * generatorFn() {}
}
```

使用yield中断执行

```javascript
function* generatorFn() {
    yield 'foo'
    yield 'bar'
    return 'baz'
}
let it = generatorFn()
console.log(it.next()) // { done: false, value: 'foo' }
console.log(it.next()) // { done: false, value: 'bar' }
console.log(it.next()) // { done: true, value: 'baz' }
```

使用yield输入输出

```javascript
function* generatorFn() {
    return yield 'foo'
}
let it = generatorFn()
console.log(it.next()) // { done: false, value: 'foo' }
console.log(it.next('bar')) // { done: true, value: 'bar' }

// 使用yield*迭代可迭代对象
function* generatorFn() {
    yield* [1, 2, 3]
}
// 等价于
function generatorFn() {
    for (const x of [1, 2, 3]) {
        yield x
    }
}
```

#### 生成器作为默认迭代器

```javascript
class Foo {
    constructor() {
        this.values = [1, 2, 3]
    }
    * [Symbol.iterator] {
        yield* this.values
    }
}
const f = new Foo()
for (const x of f) {
    console.log(x)
}
// 1, 2, 3
```

#### 提前终止生成器

##### return()

return()方法会强制生成器进入关闭状态，只要进入关闭状态，就无法恢复了，后续调用next()会显示done:true状态，for-of循环等会忽略状态为done:true时的IteratorObject内部返回的值

```javascript
function* generatorFn() {
    yield* [1, 2, 3]
}
let g = generatorFn()
console.log(g) // generatorFn {<suspended>}
console.log(g.return(4)) // { done: true, value: 4}
console.log(g) // generatorFn {<close>}
```

##### throw()

throw()方法会在暂停时将一个错误注入到生成器对象中，如果错位未被处理，则生成器就会关闭，处理了错误，则会跳过那一次执行的结果

```javascript
function* generatorFn() {
    for (const x of [1, 2, 3]) {
        try {
            yield x
        } catch(e) {}
    }
}
let g = generatorFn()
console.log(g.next()) // { done: false, value: 1 }
g.throw('foo')
console.log(g.next()) // { done: false, value: 3 }
```

### generator/yield实现async/await
