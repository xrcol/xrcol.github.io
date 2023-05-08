---
title: DOM
date: 2023-04-05 16:02:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## DOM

### 节点层级

#### Node类型

所有的DOM节点都必须实现Node接口

每个节点都存在childNodes属性，包含一个NodeList实例

NodeList不是Array的实例，但是可以像数组一样访问，同时是实时的活动对象，即DOM结构的变化会自动在NodeList中反映出来

##### 操作节点

- appendChild
- insertBefore 接受两个参数：要插入的节点和参照节点
- replaceChild 接受两个参数：要插入的节点和要替换的节点
- removeChild 接受一个参数：要移除的节点，被移除的节点会被返回

##### 其它方法

- cloneNode 接受一个参数：是否深复制，传入true，进行深复制；addEventListener和直接设置到节点上的属性不会复制(eg: node.test = 111)
- normalize 处理文档子树的文本节点：删除空的文本节点和合并相邻的文本节点

#### Document类型

##### 文档子节点

- document.documentElement 默认指向**html**元素
- document.body 默认指向**body**元素
- document.head 默认指向**head**元素

##### 文档信息

- domcument.domain只能设置为URL中包含的值，多个iframe的domain相同时，可以访问对方的Javascript对象

##### 定位元素

- getElementById 根据元素id，查找元素
- getElementsByTagName 返回也是一个实时的列表
- getElementsByName 根据元素name，查找元素

### DOM编程

#### 动态样式

```javascript
function loadStyleString(css) {
    let style = document.createElement('style')
    style.stype = 'text/css'
    try {
        style.appendChild(document.createTextNode(css))
    } catch (e) {
        style.styleSheet.cssText = css
    }
    document.head.appendChild(style)
}

```

### MutationObserver接口

#### 基本用法

```javascript
let observer = new MutationObserver((mutationRecords) => {
    console.log(mutationRecords)
})
observer.observe(document.body, { attributes: true })
```

默认情况下，只要观察的元素没有被垃圾回收，mutationObserver的回调就可以响应DOM变化事件，要提前终止回调，需要调用disconnect，调用之后不仅会停止此后变化事件的回调，同时也会抛弃已经加入任务队列中要异步执行的回调

##### 复用MutationObserver

多次调用observe，可以复用一个mutationobserver对象观察多个不用的节点

#### 观察范围

- 观察属性 attributes、attributeFilter
- 观察字符数据 characterData
- 观察子节点 childList
- 观察子树 subtree

#### 异步回调与记录队列

##### 记录队列

每次MutationRecord被添加到MutationObserver的记录队列时，仅当之前没有已排期的微任务回调时，才会将观察者注册的回调作为微任务调度到任务队列上。即虽然dom节点属性变化多次，但是回调函数仍只执行一次

##### takeRecords方法

调用takeRecords方法可以清空记录队列，取出并返回其中所有的MutationRecord实例

#### 性能、内存与垃圾回收

##### MutationObserver的引用

MutationObserver拥有对目标节点的弱引用，不会妨碍垃圾回收程序回收目标节点，然后目标节点却拥有对MutationObserver的强引用，如果目标节点从DOM中移除，被垃圾回收，则关联的MutationObserver也会被垃圾回收

##### MutationRecord的引用

记录队列中的每个MutationRecord实例至少包含对已有DOM节点的一个引用，如果变化是childList，则会包含多个节点的引用

如果需要保存某个观察者的完整变化记录，保存这些MutationRecord实例，也就保存了它们引用的节点，会妨碍这些节点被垃圾回收，如果需要尽快释放内存，可以从MutationRecord中提取有用的信息保存到新的对象中，抛弃MutationRecord
