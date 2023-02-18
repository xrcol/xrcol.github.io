---
title: basic
date: 2023-02-07 20:32:10
tags: JavaScript
categories: JavaScript高级程序设计
---

## Basic Syntax

### let and const hoisted

变量的构造大致可以分为3个阶段：创建、初始化和赋值

```javascript
fuction fn() {
    var x = 1;
    var y = 1;
}
fn();
```

x和y的声明过程：

- 进入fn，为fn创建一个执行环境
- 找到环境中所有var声明的变量，创建变量名
- 对这些变量初始化为undefined
- 开始执行代码
- 将x设置为1，y设置为1

```javascript
function fn() {
    let x = 1;
    let y = 1;
}
fn();
```

x和y的声明过程：

- 进入fn，为fn创建一个执行环境
- 找到环境中所有let声明的变量，创建变量名
- 开始执行代码
- 将x设置为1，这不是赋值，而是初始化（如果为let x，则将x初始化为undefined）