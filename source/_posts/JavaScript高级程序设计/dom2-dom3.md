---
title: DOM2和DOM3
date: 2023-04-08 16:24:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## DOM2和DOM3

### DOM的演进

#### document

domcument属性defaultView，指向当前文档的窗口

#### Node

```javascript
let div1 = document.createElement('div')
div1.setAttribute('class', 'box')
let div2 = document.createElement('div')
div2.setAttribute('class', 'box')

console.log(div1.isSameNode(div1)) // true
console.log(div1.isEqualNode(div2)) // true
console.log(div1.isSameNode(div2)) // false
```

### 样式

#### 存取元素样式

css属性float，在javascript中需要通过style.cssFloat获取

#### 元素尺寸

- 偏移尺寸 包含所有内边距、滚动条和边框 offsetHeight和offsetWidth包含了滚动条，offsetLeft、offsetTop
- 客户端尺寸 包含元素内容和内边距 clientHeight、clientWidth
- 滚动尺寸 scrollHeight、scrollWidth、scrollLeft和scrollTop
- 元素尺寸 getBoundingClientRect bottom和right

### 遍历

#### NodeIterator

```javascript
let filter = function(node) {
    return node.tagName.toLowerCase() === 'p' ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP
}
// 四个参数：作为遍历根节点的节点；数值代码，应该访问哪些节点；过滤器，是否跳过某些节点；是否扩展实体引用，html中默认为false
let iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false)
let node = iterator.nextNode()
while (node != null) {
    node  = iterator.nextNode()
}
```

#### TreeWalker

```javascript
let filter = function(node) {
    return node.tagName.toLowerCase() === 'p' ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP
}

let walker = document.createTreeWalker(root, NodeFilter.SHOW_ELEMENT, filter, false)
walker.firstChild()
walker.nextSibling()
```
