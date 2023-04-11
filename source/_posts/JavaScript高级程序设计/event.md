---
title: 事件
date: 2023-04-11 23:23:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 事件

### 事件流

#### 事件冒泡

事件被定义为从最具体的元素开始触发，向上传播至没有那么具体的元素

#### 事件捕获

最不具体的节点应该最先收到事件，而具体的节点应该最后收到事件

### 事件处理程序

```javascript
let btn = document.getElementById('myBtn')
btn.addEventListener('click', function() {
    console.log('propagation trigger')
})
btn.addEventListener('click', function() {
    console.log('capture trigger')
}, true)
```

### 事件对象

#### DOM事件对象

##### eventPhase

- 1表示捕获阶段
- 2表示到达目标 此时target和currentTarget相等
- 3表示冒泡阶段

##### stopPropagation和stopImmediatePropagation

stopImmediatePropagation不仅阻止父级节点事件的触发，同时还阻止当前元素，同事件类型的触发

stopPropagation阻止父级节点事件的触发

### 事件类型

#### 用户界面事件

##### load事件

```javascript
window.addEventListener('load', () => {
    let image = new Image()
    image.addEventListener('load', (event) => {
        console.log('Image loaded')
    })
    image.src = 'smile.gif' // 下载图片并不一定需要将img元素添加到文档，只要设置了src属性，就会立即开始下载

    let script = document.createElement('script')
    script.addEventListener('load', (event) => {
        console.log('script loaded')
    })
    script.src = 'example.js'
    document.body.appendChild(script) // 下载javascript文件必须同时指定src属性，并把script元素添加到文档，才会开始下载
})
```

##### resize事件

默认窗口缩放超过1px时触发resize事件

#### 焦点事件

focusin和focusout分别是focus和blue的冒泡版本

当焦点从页面的一个元素移动到另一个元素时：

- focusout在失去焦点的元素上触发
- focusin在获得焦点的元素上触发
- blur在失去焦点的元素上触发
- focus在获得焦点的元素上触发

#### 鼠标事件

```javascript
// 客户端坐标
event.clientX
event.clientY
// 页面坐标，当没有滚动时，与客户端坐标相同
event.pageX
event.pageY
// 屏幕坐标
event.screenX
event.screenY
// 鼠标按键
event.button // 0表示鼠标左键，1表示鼠标中键，2表示鼠标右键
```

#### HTML5事件

##### DOMContentLoaded事件

window的load事件会在页面完全加载后触发，需要等待外部资源的加载完成，而DOMContentLoaded事件会在DOM树构建完成后立即触发，不需要等待外部资源的加载

##### hashchange事件

当URL散列值发生变化时通知开发者，必须添加给window

```javascript
window.addEventListener('hashchange', (event) => {
    console.log(`Old URL: ${event.oldURL}, New URL: ${event.newURL}`)
    console.log(location.hash)
})
```

### 内存和性能

事件委托和删除事件处理程序

给所有元素共同的祖先节点添加一个事件处理程序

- document对象随时可用
- 减少整个页面所需的内存，提升整体性能

### 模拟事件

```javascript
let event = document.createEvent('CustomEvent')
// type 要触发的事件类型
// bubbles 表示事件是否冒泡
// cancelable 表示事件是否可以取消
// detail 任意值
event.initCustomEvent('myevent', true, false, 'hello')
div.dispatchEvent(event)
```
