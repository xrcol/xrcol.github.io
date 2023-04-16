---
title: 表单脚本
date: 2023-04-16 15:35:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 表单脚本

### 表单基础

```javascript
let form = document.getElementById('form1')
form.acceptCharset // 服务器可以接受的字符集 ，等价于HTML的accept-charset属性
form.action // 请求的URL
form.elements // 表单中所有控件
form.enctype // 请求的编码类型
form.length // 表单中控件的数量
form.method // HTTP请求的方法类型
form.name // 表单的名字
form.reset() // 把表单字段重置为各自的默认值
form.submit() // 提交表单
form.target // 用于发送请求和接受响应的窗口的名字
```

#### 表单字段

```javascript
let field1 = form.elements[0]
let field2 = form.elements['test']
let fieldCount = form.elements.length
```

##### 表单字段的公共事件

change: 在\<input\>和\<textarea\>元素的value发生变化且失去焦点时触发，或者在\<select\>元素选中项发生变化时触发

### 文本框编程

#### 输入过滤

```javascript
// 屏蔽非数字字符
textbox.addEventListener('keypress', (e) => {
    if (!/\d/.test(String.fromCharCode(event.charCode)) && event.charCode > 9 && !e.ctrlKey) {
        e.preventDefault()
    }
})
```

### 富文本编辑

#### contenteditable

```html
<div class="editable" id="richedit" contenteditable>

</div>
```

#### 与富文本交互

```javascript
// 切换粗体文本样式
document.execCommand('bold', false, null)
```
