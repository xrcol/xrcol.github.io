---
title: DOM扩展
date: 2023-04-05 18:05:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## DOM扩展

### Selectors API

- querySelector 未找到则返回null
- querySelectorAll 未找到则返回空的NodeList实例（静态的NodeList，不是实时的）

### 元素遍历

为了避免将元素间的空格当成空白节点，导致childNodes和firstChild属性上的差异，定义了新的属性childElementCount、firstElementChild、previousElementSibling

### HTML5

#### CSS类扩展

##### getElementsByClassName

根据类名，查找元素

##### classList属性

```javascript
div.classList.remove('user')
div.classList.add('user')
div.classList.contains('user')
div.classList.toggle('user') // 没有则添加，有则删除
```

##### 焦点管理

document.activeElement，页面加载完成时会设置为document.body

##### 自定义数据属性

```javascript
<div id="main" data-appId="111" data-my-name="test">

</div>
let div = document.getElementById('main')
let appId = div.dataset.appId
let myName = div.dataset.myName
```

##### scrollIntoView

```javascript
document.forms[0].scrollIntoView()
document.forms[0].scrollIntoView(true, { block: 'start' })
```

alignToTop

- true 窗口滚动后元素的顶部与视口顶部对齐
- false 窗口滚动后元素的底部与视口底部对齐

scrollIntoViewOptions

- behavior 定义过渡动画，可取的值为'smooth'和'auto'，默认为'auto'
- block 定义垂直方向的对齐，可取的值为'start'、'center'、'end'和'nearest'，默认为'start'
- inline 定义水平方向的对齐

### 专有扩展

#### contains方法

```javascript
console.log(document.documentElement.contains(document.body)) // true
```

#### 插入标记

##### innerText属性

innerText属性对应元素中包含的所有文本内容，无论文本在子树的哪个层级，在读取值时，innerText会按照深度优先的顺序将子树中所有文本节点的值拼接起来

innerText写入内容时，会移除元素所有的后代并插入一个包含该值的文本节点，同时也会进行编码

##### outerText属性

outerText写入内容时，不仅会移除所有的后代节点，而是替换整个元素
