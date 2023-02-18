---
title: script
date: 2023-02-07 20:27:24
tags: JavaScript
categories: JavaScript高级程序设计
---

## Script加载

### 异步加载

```javascript
<script async src='test.js'></script>
```

### 延迟加载

```javascript
<script defer src='test.js'></script>
```

### 同步加载

```javascript
// 设置async为false来实现同步加载
let script = document.createElement('script')
script.src = 'test.js'
script.async = false
document.head.appendChild(script)
```

| difference | async | defer |
| :----------------------------------------------------------: | :-----------------------------------------: | :-----------------------------------------: |
|                      block page loading                      |                     no                      |                     no                      |
|                   execute by insert order                    |                     no                      |                     yes                     |
|                       rely on the DOM                        |                     no                      |                     yes                     |

 ![script](https://html.spec.whatwg.org/images/asyncdefer.svg)

### 场景

**async**下载完后立即执行，适用于要尽可能快的执行脚本

**defer**下载完后也需要等到文档解析完执行，适用于脚本中需要操作DOM

### 预加载

不会执行，只是下载和缓存

```javascript
<link rel='preload' href='test.js' as='script' />
<link rel='preload' href='test.css' as='stylesheet' />
```

### 参考链接

[1] <https://html.spec.whatwg.org/multipage/scripting.html#attr-script-defer/>
