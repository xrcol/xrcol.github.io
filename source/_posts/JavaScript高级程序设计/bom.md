---
title: BOM
date: 2023-04-02 23:03:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## BOM

### window对象

#### 窗口关系

- window.top始终指向最上层窗口
- window.parent始终指向当前窗口的父窗口
- window.self始终指向自身

#### 窗口位置和像素比

- window.screenLeft、window.screenX表示窗口相对于屏幕左侧的距离
- window.screenTop、window.screenY表示窗口相对于屏幕顶部的距离
- moveTo()和moveBy()，移动窗口的位置
- window.devicePixelRatio表示物理像素与逻辑像素之间的缩放系数

#### 窗口大小

- innerWidth和outerWidth
- resizeTo和resizeBy缩放到固定的大小

#### 视口位置

- window.pageXoffset和window.scrollX
- window.pageYoffset和window.scrollY
- scrollTo、scrollBy滚动距离

```javascript
window.scrollTo({
    left: 100,
    top: 100,
    behavior: 'auto' // 'smooth'
})
```

### location对象

```javascript
location.href // "http://www.wrox.com:80/WileyCDA/?q=javascript#contents"
location.hash // "#contents"
location.host // "www.wrox.com:80"
location.hostname // "www.wrox.com"
location.pathname // "/WileyCDA"
location.port // "80"
location.protocol // "http"
location.search // "?q=javascript"
location.origin // "http://www.wrox.com"
let qs = "?q=javascript"
let params = new URLSearchParams(qs) // "q=javascript"
params.set('name', 'test')
params.toString() // q=javascript&name=test
```

#### 操作地址

```javascript
// 修改浏览器地址
location.assign("http://www.wrox.com")
location.href = "http://www.wrox.com";
// 除了hash之外，只要修改location的一个属性，就会导致页面重新加载URL，同时浏览器历史记录中会增加相应的记录
location.replace("http://www.wrox.com") // 不会增加历史记录，即不能回到前一页
location.reload() //重新加载页面，已最有效的方式
location.reload(true) // 重新加载页面，从服务器加载
```

### history对象

#### 导航

```javascript
history.go(-1) // 后退一页
history.go(1) // 前进一页
history.back() // 后退一页
history.forward() // 前进一页
```

#### 状态管理

```javascript
let state = { foo: 'bar' }
history.pushState(state, 'new title', 'baz.html') // state对象(500kb~1MB)、新状态的标题和（可选的）相对URL
```

执行pushState后，状态信息会被推送到历史记录，同时浏览器地址栏会改变以反映新的相对URL，浏览器不会向服务器发起请求

```javascript
// 点击后退按钮时，触发window上的popstate事件
window.addEventListener("popstate", (event) => {
    let state = event.state
    if (state) { // 第一个页面加载时状态为null
        processState(state)
    }
})
history.replaceState({ foo: 'bar'}, 'new title') // 不会创建历史记录，只会覆盖当前状态

```

state对象中应该只包含可以被序列化的信息，同时在使用HTML5状态管理时，要确保通过pushState()创建的每个“假”URL背后都对应着服务器上一个真实的物理URL，否则单击刷新会导致404错误，所有的SPA框架都必须通过某些配置解决这个问题
