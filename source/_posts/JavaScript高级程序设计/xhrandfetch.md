---
title: 网络请求与远程资源
date: 2023-04-30 23:20:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 网络请求与远程资源

### XMLHttpRequest对象

#### 使用XHR

```javascript
let xhr = new XMLHttpRequest()
// 同步请求的处理方式
xhr.open('get', 'https://www.baidu.com', false)
xhr.send(null)

if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    console.log(xhr.responseText)
} else {
    console.log('Request was unsuccessful: ' + xhr.status)
}
// 异步请求的处理方式
xhr.onreadystatechange = function() { // 先添加事件监听
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText)
        } else {
            console.log('Request was unsuccessful: ' + xhr.status)
        }
    }
}
xhr.open('get', 'https://www.baidu.com', true)
xhr.send(null)
// 收到响应前，想取消异步请求
xhr.abort()

// xhr.readyState
// 0 -- 未初始化，尚未调用open方法
// 1 -- 已打开，已调用open方法，未调用send方法
// 2 -- 已发送，已调用send方法，尚未收到响应
// 3 -- 接受中，已收到部分响应
// 4 -- 完成，已经收到所有响应，可以使用了
```

#### HTTP头部

setRequestHeader自定义请求头部信息，必须在open之后、send之前调用

getResponseHeader获取响应头部

getAllResponseHeaders获取所有响应头部

#### GET请求

发送GET请求，查询字符串的key和value都必须使用encodeURIComponent()编码

```javascript
function addURLParam(url, name, value) {
    url += url.indexOf('?') == -1 ? '?' : '&'
    url += encodeURIComponent(name) + '=' + encodeURIComponent(value)
    return url
}
let url = 'example.php'
url = addURLParam(url, 'name', 'Nicholas')
url = addURLParam(url, 'book', 'JavaScript')
xhr.open('get', url, false)
```

#### POST请求

```javascript
// 模拟表单提交
xhr.open('post', 'example.php', true)
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
xhr.send()
```

#### XMLHttpRequest Level 2

##### FormData类型

```javascript
// 使用FormData，则不再需要显示设置请求头部了
let form = document.getElementById('user-info')
xhr.send(new FormData(form))

let data = new FromData()
data.append('name', 'Nicholas')
```

##### 超时

```javascript
xhr.timeout = 1000 // 设置1秒超时
xhr.ontimeout = function() {
    console.log('request did not resturn in a second')
}
// 超时，readyState会变为4，因此也会调用onreadystatechange事件处理程序，但是访问status属性会发生错误
```

##### overrideMimeType方法

假设服务器实际发送了XML数据，但是响应头设置的MIME类型是text/plain，导致虽然数据是XML，但responseXML仍为null，此时需要调用overrideMimeType()保证将响应当成XML处理

```javascript
let xhr = new XMLHttpRequest()
xhr.open('get', 'text.php', true)
xhr.overrideMimeType('text/xml') // 确保将响应当作xml处理，必须在send之前调用
xhr.send(null)
```

### 进度事件

#### load事件

只要从服务器收到响应，无论状态码是什么，都会触发load事件，这样就不用检查readyState属性

```javascript
xhr.onload = function() {
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        console.log(xhr.responseText)
    } else {
        console.log('request was unsuccessful: ' + xhr.status)
    }
}
```

#### progress事件

```javascript
xhr.onprogress = function(event) { // 必须在open之前添加
    if (event.lengthComputable) { // 表示进度信息是否可用
        console.log(event.position) // 接受到的字节数
        console.log(event.totalSize) // 响应的Content-Length头部定义的总字节数
    }
}
```

### 跨源资源共享

对于简单的请求，GET或POST请求，没有自定义头部，同时请求体是text/plain类型，这时请求在发送时会有一个额外的头部叫Origin，Origin头部包含发送请求的页面的源（协议、域名和端口），以便服务器确定是否为其提供响应

如果服务器决定响应请求，则会发送Access-Control-Allow-Origin头部，包含相同的源；如果资源公开，则包含*。如果没有这个头部或者有但源不匹配，则表明不会响应浏览器请求。（无论请求还是响应都不会包含cookie信息

#### 预检请求

CORS通过一种预检请求的服务器验证机制，允许使用自定义头部、除GET和POST之外的方法，以及不同请求体内容类型。在发送设计上述高级选项的请求时，会先向服务器发送一个“预检”请求

使用options方法发送，并包含以下头部：

- Origin: 与简单请求相同
- Access-Control-Request-Method: 请求希望使用的方法
- Access-Control-Request-Headers: 要使用的逗号分隔的自定义头部列表

服务器会在响应中发送如下头部：

- Access-Control-Allow-Origin: 与简单请求相同
- Access-Control-Allow-Methods: 允许的方法
- Access-Control-Allow-Headers: 服务器允许的头部（逗号分隔的列表）
- Access-Control-Max-Age: 缓存预检请求的秒数

预检请求返回后，结果会按响应指定的时间缓存一段时间，即只有第一个发送这种类型的请求时才会多发送一次额外的HTTP请求（默认缓存5秒）

#### 凭据请求

将请求的withCredentials属性设置为true表明请求会发送凭据，如果服务器允许这种请求，则会在响应中包含

Access-Control-Allow-Credentials: true

### 替代性跨源技术

#### 图片探测

```javascript
let img = new Image()
img.onload = img.onerror = function() {
    console.log('done')
}
img.src = 'http://www.example.com/test?name=test'
```

图片探测的缺点是只能发送GET请求和无法获取服务器响应的内容，即利用图片探测只能实现浏览器与服务器单向通信

#### JSONP

script和img类似，可以不受限制地从其它域加载资源

```javascript
function handleResponse(response) {
    console.log(response.id)
}
let script = document.createElement('script')
script.src = 'http://freegeoip.net/json/?callback=handleResponse'
document.body.appendChild(script)
```

需要从不同的域拉取可执行代码，响应中可能存在恶意内容

不好确定JSONP请求是否失败

### Fetch API

#### 基本用法

```javascript
fetch('bar.txt').then(response => {
    console.log(response.url) // 请求的url地址
    console.log(response.redirected) // 是否重定向
    return response.text()
}, err => {
    console.log(err) // 违反CORS、无网络连接、HTTPS配错等问题都会导致promise拒绝
}).then(data => console.log(data))
```

#### 常见Fetch请求模式

```javascript
// 发送json数据
let payload = JSON.stringify({
    foo: 'bar'
})
let headers = new Headers({
    'Content-Type': 'application/json'
})
fetch('/send', {
    method: 'POST',
    body: payload,
    headers: headers
})

// 中断请求
let abortController = new AbortController()
fetch('/wiki', {
    signal: abortController.signal
}).catch(() => console.log('aborted'))
setTimeout(() => abortController.abort(), 10)
```

#### Headers对象

Headers与Map用法基本相同，但是初始化Headers对象时，可以直接使用键值对的对象。append()方法支持添加多个值

```javascript
let seed = { foo: 'bar' }
let header = new Headers(seed)

header.append('foo', 'baz')
console.log(header.get('foo')) // 'bar, baz'
```

#### Request对象

```javascript
// 创建Request对象
let request = new Request('http://test.com', { method: 'POST' })
// 克隆Request对象
let r1 = new Request('http://test.com', { method: 'post', body: 'foobar' })
let r2 = new Request(r1) // 第一个请求的请求体会被标记为已使用r1.bodyUsed-true
let r3 = r1.clone() // 创建一模一样的副本，如果创建前r1的请求体已经被使用，则无法克隆
```

### Beacon API

在unload事件处理程序中创建的任何异步请求都会被浏览器取消

```javascript
navigator.sendBeacon('https://test.com', '{ foo: "bar" }')
```

sendBeacon发送POST请求，同时在任何时候都可以使用

调用sendBeacon后，浏览器会把请求添加到内部的请求队列，之后主动发送请求

默认会携带所有相关的cookie

### Web Socket

#### API

```javascript
let ws = new WebSocket('ws://www.example.com')
console.log(ws.readyState)
// 0 连接正在建立
// 1 连接已经建立
// 2 连接正在关闭
// 3 连接已经关闭
ws.close()
```

#### 发送和接受数据

```javascript
let str = 'hello world'
let buffer = Uint8Array.from(['f', 'o', 'o'])
let blob = new Blob(['f', 'o', 'o'])
ws.send(str)
ws.send(buffer)
ws.send(blob)
ws.onmessage = function(event) {
    console.log(event.data)
}
```

#### 其它事件

```javascript
// 连接成功建立时触发
ws.onopen = function() {
    console.log('connection established')
}
// 发生错误时触发
ws.onerror = function() {
    console.log('connection error')
}
// 连接关闭时触发
ws.onclose = function() {
    console.log('connection closed')
}
```
