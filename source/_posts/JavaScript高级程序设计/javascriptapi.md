---
title: 表单脚本
date: 2023-04-16 15:35:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## JavaScript API

### Atomics和SharedArrayBuffer

#### SharedArrayBuffer

SharedArrayBuffer与ArrayBuffer具有同样的API，但是ArrayBuffer必须在不同执行上下文间切换，而SharedArrayBuffer可以被任意多个执行上下文同时使用

#### 原子操作基础

```javascript
let sharedArrayBuffer = new SharedArrayBuffer(1)
let typedArray = new Unit8Array(sharedArrayBuffer)
const index = 0
const increment = 5
Atomics.add(typedArray, index, increment)
Atomics.sub(typedArray, index, increment)

Atomics.load(view, 0) // 原子读
Atomics.store(view, 0, 2) // 原子写

Atomics.exchange(view, 0, 4) // 先读取索引0处的值，再写入4
Atomics.compareExchange(view, 0, initial, result) // 当目标索引处的值与预期值匹配时，才执行写操作

Atomics.wait(view, 0, 0, 1E4) // 如果索引处的值为0，则停止，超时时间为10000
Atomics.notify(view, 0, 1) // 唤醒一个线程
```

### 跨上下文消息

```javascript
window.addEventListener('message', (event) => {
    if (event.origin === 'xxx') {
        processMessage(event.data)
        event.source.postMessage('received!', 'origin url')
    }
})
```

### Encoding API

实现字符串和定型数组之间的转换

#### 文本编码

##### 批量编码

```javascript
const textEncoder = new TextEncoder()
const decodedText = 'foo'
const encodedText = textEncoder.encode(decodedText) // Uint8Array返回每个字符的UTF-8编码

const fooArr = new Uint8Array(3)
const fooResult = textEncoder.encodeInto('foo', fooArr)
console.log(fooResult) // { read: 3, written: 3 }
```

##### 流编码

#### 文本解码

##### 批量解码

```javascript
const textDecoder = new TextDecoder()
const encodedText = Uint8Array.of(102, 111, 111)
const decodedText = textDecoder.decoder(encodedText)
console.log(decodedText) // foo
```

##### 流解码

### File API与Blob API

#### File类型

```javascript
let filesList = document.getElementById('files-list')
filesList.addEventListener('change', (event) => {
    let files = event.target.files
    let i = 0
    let len = files.length
    while (i < len) {
        const f = files[i]
        console.log(`${f.name} ${f.type} ${f.size} bytes`)
        i++
    }
})
```

#### FileReader类型

一种异步读取文件机制

```javascript
let filesList = document.getElementById('files-list')
filesList.addEventListener('change', (event) => {
    let info = ''
    output = document.getElementById('output')
    progress = document.getElementById('progress')
    files = event.target.files
    type = 'default'
    reader = new FileReader()
    if (/image/.test(files[0].type)) {
        reader.readAsDataURL(files[0])
        type = 'image'
    } else {
        reader.readAsText(files[0])
        type = 'text'
    }

    reader.onerror = function() {
        output.innerHTML = 'Could not read file, error code is ' + reader.error.code
    }
    // progress每50毫秒触发一次
    reader.onprogress = function() {
        if (event.lengthComputable) {
            progress.innerHTML = `${event.loaded} / ${event.total}`
        }
    }
    reader.onload = function() {
        let html = '*'
        switch(type) {
            case 'image':
                html = `<img src="${reader.result}">`
                break
            case 'text':
                html = reader.result
                break
        }
        output.innerHTML = html
    }

})
```

#### FileReaderSync类型

FileReader的同步版本，只有在整个文件都加载到内存后，才会继续执行。FileReaderSync只有在工作线程可用，因为如果读取整个文件太耗时则会影响全局

```javascript
// 使用postMessage向工作线程发送了一个File对象
self.onmessage = (event) => {
    const syncReader = new FileReaderSync()
    const result = syncReader.readAsDataURL(event.data)
    console.log(result)
    self.postMessage(result)
}
```

#### Blob与部分读取

```javascript
function * slice(file) {
    const MB = 1024 * 1024
    let i = 0
    let start = 0
    while (true) {
        const end = start + MB
        const blob = file.slice(start, end) // 文件切片
        if (blob.size() === 0) break
        yield { chunk: i++, blob }
        start = end
    }
}
```

#### 对象URL与Blob

```javascript
let filesList = document.getElementById('files-list')
filesList.addEventListener('change', (event) => {
    output = document.getElementById('output')
    progress = document.getElementById('progress')
    files = event.target.files
    url = window.URL.createObjectURL(files[0]) // 返回一个指向内存中地址的字符串
    if (url) {
        output.innerHTML = `<img src="${url}">`
    }
}
```

只要对象URL还在使用，就不能释放内存，如果不再使用对象URL，则可以传给window.URL.revokeObjectURL()。

### 原生拖放

#### 拖放事件

在某个元素被拖动时，会依次触发dragstart、drag和dragend

在按住鼠标不放并移动鼠标的那一刻，被拖动元素上会触发dragstart事件，dragstart事件触发后，只要目标还在拖动就会持续触发drag事件，当拖动停止时（把元素放到有效或无效的放置目标上），会触发dragend事件，3个事件的目标都是被拖动的元素

把元素拖动到一个有效的放置目标，会依次触发dragenter、dragover和(dragleave或drop)

只要把元素拖动到放置目标上，dragenter事件就会触发，dragenter事件触发之后，会立即触发dragover事件，并且在放置目标范围内拖动期间会持续触发。当元素被拖动到放置目标之外，dragover事件停止触发，dragleave事件触发，如果元素被拖动到了目标上，会触发drop事件，而不是dragleave事件

#### 自定义放置目标

```javascript
// 将该元素转换为放置目标
let droptarget = document.getElementById('droptarget')
droptarget.addEventListener('dragover', (e) => {
    e.preventDefault()
})
droptarget.addEventListener('dragenter', (e) => {
    e.preventDefault()
})
// Firefox中，放置事件的默认行为是导航到放在放置目标上的URL，把图片拖动到放置目标上会导致页面导航到图片文件
// 必须取消默认行为
droptarget.addEventListener('drag', (e) => {
    e.preventDefault()
})
```

#### dataTransfer对象

所有拖动事件event都可以访问dataTransfer属性

```javascript
event.dataTransfer.setData('text', 'test')
let text = event.dataTransfer.getData('text')
```

#### dropEffect与effectAllowed

必须在dragstart事件中设置这个属性

##### dropEffect

- 'none': 被拖动元素不能放到这里。除文本框之外所有元素的默认值
- 'move': 被拖动元素应该移动到放置目标
- 'copy': 被拖动元素应该复制到放置目标
- 'link': 表示放置目标会导航到被拖动元素

##### effectAllowed

表示被拖动元素是否允许dropEffect

#### 可拖动能力

```html
// 设置元素的draggable属性
<div draggable='true'>

</div>
```

### Notification API

#### 通知权限

```javascript
Notification.requestPermission().then((permission) => {
    if (permission === 'granted') {

    } else {

    }
})
console.log(window.Notification.permission)
```

#### 显示和隐藏通知

```javascript
const n = new Notification('title text', {
    body: 'body text',
    image: 'image.png',
    vibrate: true // 震动
})
setTimeout(() => n.close(), 1000)
```

#### 通知声明周期回调

```javascript
const n = new Notification('foo')
n.onshow = () => console.log('notification was shown')
n.onclick = () => console.log('notification was clicked')
n.onclose = () => console.log('notification was closed')
n.onerror = () => console.log('notification experienced an error')
```

### Page Visibility API

```javascript
document.addEventListener('visibilitychange', () => {
    console.log(document.visibilityState) // visible or hidden
})
```

### Streams API

### 计时API

performance.now()计时器采用相对度量，在执行上下文创建时从0开始计时。例如，打开页面或创建工作线程时，performance.now()会从0开始计时

performance.timeOrigin属性返回计时器初始化时全局系统时钟的值

```javascript
const relativeTimestamp = performance.now()
const absoluteTimestamp = performance.timeOrigin + relativeTimestamp
```

### Web组件

#### 影子DOM

##### 基础使用

```javascript
const div = document.createElement('div')
const shadowDOM = div.attachShadow({ mode: 'open' })
document.body.appendChild(div)
shadowDOM.innerHTML = '<p> test dom </p>'
```

##### 影子DOM槽位

```javascript
const div = document.createElement('div')
const shadowDOM = div.attachShadow({ mode: 'open' })
document.body.appendChild(div)
div.innerHTML = '<p>Foo</p><p slot="bar">Bar</p>'
shadowDOM.innerHTML = '<div id="bar" > <slot></slot> <slot name="bar"></slot></div>'
```

##### 自定义元素

```javascript
class FooElement extends HTMLDivElement {
    constructor() {
        super()
        this.attachShadow({ mode: 'open' })
        this.shadowRoot.innerHTML = '<p>I am inside a custom element!</p>'
    }
    connectedCallback() { // 将自定义元素添加到DOM中时调用

    }
    disconnectedCallback() { // 将自定义元素从DOM中移除时调用

    }
    attributeChangedCallback() { // 在每次可观察属性的值发生变化时调用。在元素实例初始化时，初始值的定义也算一次变化

    }
    adoptedCallback() { //

    }
}
customElements.define('x-foo', FooElement, { extends: 'div' })
document.body.innerHTML = '<div is="x-foo"></div>'
```
