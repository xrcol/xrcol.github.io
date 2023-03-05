---
title: 代理与反射
date: 2023-03-05 21:58:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## ✨代理与反射✨

### 代理基础

#### 基础用法

```javascript
const target = {
    id: 'target'
}
const proxy = new Proxy(target, {})
console.log(target.id) // target
console.log(proxy.id) // target
```

#### 捕获器

```javascript
const target = {
    foo: 'bar'
}
const handler = {
    get(target, property, receiver) {
        return Reflect.get(...arguments)
    }
}
```

#### 可撤销的代理

```javascript
const target = {
    foo: 'bar'
}
const handler = {
    get(target, property, receiver) {
        return 'intercepted'
    }
}
const { proxy, revoke } = Proxy.revocable(target, handler)
console.log(proxy.foo) // intercepted
console.log(target.foo) // bar
revoke()
console.log(proxy.foo) // TypeError
```

#### 代理的不足

- 代理中的this

- 代理与内部槽位，比如Date类型，Date类型方法的执行依赖this值上的内部槽位，由于代理对象上不存在内部槽位，导致代理拦截后转发给目标对象的方法抛出TypeError

### 可代理的方法

```javascript
const target = {}
const proxy = new Proxy(target, {
    get(target, property, receiver) {
        return Reflect.get(...arguments)
    },
    set(target, property, value, receiver) {
        return Reflect.set(...arguments)
    },
    has(target, property) {
        return Reflect.has(...arguments)
    },
    defineProperty(target, property, descriptor) {
        return Reflect.defineProperty(...arguments)
    },
    getOwnPropertyDescriptor(target, property) {
        return Reflect.getOwnPropertyDescriptor(...arguments)
    },
    deleteProperty(target, property) {
        return Reflect.deleteProperty(...arguments)
    },
    ownKeys(target) {
        return Reflect.ownKeys(...arguments)
    },
    getPrototypeOf(target) {
        return Reflect.getPrototypeOf(...arguments)
    },
    setPrototypeOf(target, prototype) {
        return Reflect.setPrototypeOf(...arguments)
    },
    apply(target, thisArg, ...argumentsList) {
        return Reflect.apply(...arguments)
    },
    construct(target, argumentsList, newTarget) {
        return Reflect.construct(...arguments)
    }
})
```

### 代理模式

- 跟踪属性的访问
- 隐藏属性
- 属性验证

- 函数与构造函数参数验证
- 数据绑定和可观察对象
