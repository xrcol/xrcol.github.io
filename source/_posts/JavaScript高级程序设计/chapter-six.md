---
title: 集合引用类型
date: 2023-02-18 22:22:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 第六章 集合引用类型

### Array

#### 创建数组

通过new 方法创建

```javascript
const a = new Array(5)
```

Array.from将可迭代结构或者存在length属性和可索引元素的结构转为数组

```javascript
const a = [1, 2, 3, 4]
Array.from(a)
Array.from(a, x => x * 2) // 第二个参数增强新数组
Array.from(a, x => x * this.exponent, {exponent: 2}) // 第三个参数指定this的值
```

Array.of将一组参数组合为数组

```javascript
Array.of(1, 2, 3, 4)
```

### 判断是否是数组

```javascript
Array.isArray(value)
```

### 数组迭代器

```javascript
const a  = Array.of("foo", "bar", "baz")
Array.from(a.keys()) // [0, 1, 2]
Array.from(a.values()) // ["foo", "bar", "baz"]
Array.from(a.entries()) // [[0, "foo"], [1, "bar"], [2, "baz"]]
```

### 操作方法

使用Symbol.isConcatSpreadable=false，来使concat不会打平数组

```javascript
const alpha = ['a', 'b', 'c'];
const numeric = [1, 2, 3];
let alphaNumeric = alpha.concat(numeric);
console.log(alphaNumeric);
// Expected output: Array ["a", "b", "c", 1, 2, 3]

numeric[Symbol.isConcatSpreadable] = false;
alphaNumeric = alpha.concat(numeric);
console.log(alphaNumeric);
// Expected output: Array ["a", "b", "c", [1, 2, 3]]
```

slice方法，创建一个包含原数组元素的新数组

```javascript
slice(start, end) // 参数为负值，则其实际的值为数组长度加上负值
const nums = [1, 2, 3]
nums.slice(-2, -1) => nums.slice(1, 2) => [ 2 ]
```

splice方法，对原数组进行添加/删除元素

```javascript
splice(start, deleteCount, insertElement)
const nums = [1, 2, 3, 4, 5]
nums.splice(0, 2) // nums: [3, 4, 5]
nums.splice(0, 2, 6, 7) // nums: [6, 7, 3, 4, 5]
```

### 迭代方法

every(): 对数组每一项都运行传入的函数，如果每一行函数返回true，则这个方法返回true

```javascript
const nums = [1, 2, 3, 4, 5, 6, 7]
const res = nums.every((item, index, array) => item > 2) // res: false
```

filter(): 对数组每一项运行传入的函数，函数返回为true的项会组成新的数组

```javascript
const nums = [1, 2, 3, 4, 5, 6, 7]
const res = nums.filter((item, index, array) => item > 2) // res: [3, 4, 5, 6, 7]
```

map(): 对数组每一项运行传入的函数，返回由每次函数调用结果组成的数组

```javascript
const nums = [1, 2, 3, 4, 5, 6, 7]
const res = nums.map((item, index, array) => item * 2) // res: [3, 4, 5, 6, 7]
```

some(): 对数组每一项运行传入的函数，如果有一项函数返回true，则这个方法返回true

```javascript
const nums = [1, 2, 3, 4, 5, 6, 7]
const res = nums.some((item, index, array) => item > 2) // res: true
```

### Map

#### 基础方法

```javascript
const map = new Map([
    ["key1", "value1"],
    ["key2", "value2"],
])
map.has("key1") // true
map.get("key1") // value1
map.set("key3", "value3")
map.size() // 2
map.delete("key1")
map.clear()
// 迭代
for (let pair of map.entries()) {}
for (let key of map.keys()) {}
for (let value of map.values()) {}
[...map] // [["key1", "value1"], ["key2", "value2"]]
```

#### Map和Object区别

- 内存占用，固定大小内存，Map大约可以比Object多存储50%的键值对
- 插入性能，消耗大致相当，不过Map在浏览器中会稍微快一点
- 查找速度，当把Object当作数组使用（连续整数作为属性），浏览器会进行优化，查找速度会快一点
- 删除性能，使用Map

### WeakMap

弱映射中的键只能是object或者继承object的类型，弱映射不会阻止垃圾回收程序

```javascript
const wm = new WeakMap()
const key1 = {id: 1}
const key2 = {id: 2}
wm.has(key1) // false
wm.get(key1) // undefined
wm.set(key1, "test")
wm.delete(key1)
```