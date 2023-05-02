---
title: 客户端存储
date: 2023-05-02 10:42:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 客户端存储

### Cookie

#### 限制

- 不超过300个cookie
- 每个cookie不超过4096字节，超出限制，则静默删除
- 每个域不超过20个cookie
- 每个域不超过81920字节

#### cookie的构成

```javascript
Set-Cookie: name=value; expires=xxx; domain=.wrox.com; path=/; secure
```

名称和值必须经过URL编码

域：cookie有效的域，发送到这个域的所有请求都会包含对应的cookie

路径：请求URL中包含这个路径才会把cookie发送到服务器

过期时间：表示何时删除cookie的时间戳

安全标志：设置之后，只在使用SSL安全连接的情况下才会把cookie发送到服务器

#### JavaScript中的cookie

```javascript
document.cookie = encodeURIComponent('name') + "=" + encodeURIComponent('test')
// 获取cookie，需要调用decodeURIComponent进行解码
```

#### 子cookie

为绕过浏览器对每个域cookie数的限制，在单个cookie中存储多个键值对

```javascript
name=name1=value1&name2=value2&name3=value3&name4=value4=name5=value5
```

#### 使用cookie的注意事项

http-only的cookie，设置后只能在服务器上读取，前端读取直接返回空字符串

```javascript
Set-Cookie: name=value; expires=xxx; domain=.wrox.com; path=/; secure; HTTPOnly;
```

### Web Storage

#### Storage类型

只能存储字符串，非字符串数据在存储之前会自动转换为字符串

#### sessionStorage对象

sessionStorage只存储会话数据，即数据只会存储到浏览器关闭

```javascript
for (let key in sessionStorage) {
    let value = sessionStorage[key]
    console.log(value)
}
```

#### localStorage对象

要访问同一个localStorage对象，页面必须来自同一个域（子域不可以）、在相同的端口上使用相同的协议

localStorage数据不受页面刷新影响，也不会因为关闭窗口、标签页或重启浏览器而丢失。除非主动调用删除方法或者清除浏览器缓存

#### 存储事件

对于sessionStorage和localStorage上的任何更改都会触发storage事件

```javascript
window.addEventListener('storage', (event) => {
    console.log(event.domain) // 存储变化的域
    console.log(event.key) // 被设置或删除的键
    console.log(event.newValue) // 键被设置的新值，若键被删除则为null
    console.log(event.oldValue) // 键变化之前的值
})
```

#### Storage限制

大多数限制为每个源5MB

### IndexedDB

#### 数据库

如果给定的数据库已存在，则会发送一个打开它的请求；如果不存在，则会发送创建并打开这个数据库的请求

```javascript
let db, request, version = 1
request = indexedDB.open('admin', version)
request.onerror = (event) => console.log('Failed to open ' + event.target.errorCode)
request.onsuccess = (event) => db = event.target.result
```

#### 事务

```javascript
let transaction = db.transaction('users', 'readwrite')
let store = transaction.objectStore('users')
request = store.get('007')
request.onerror = (event) => console.log('Did not get the object')
request.onsuccess = (event) => console.log(event.target.result.firstName)
```

#### 插入对象

已存在同名的键时，add()会导致错误，put()会简单地重写该对象

#### 索引

```javascript
index = store.createIndex('indexname', 'attributename', { unique: true })
request = index.openCursor()
```

#### 并发问题

两个不同的浏览器标签页打开同一个网页，可能会出现一个网页尝试升级数据库而另一个尚未就绪的情形

```javascript
request = indexedDB.open('admin', 1)
request.onsuccess = (event) => {
    database = event.target.result
    database.onversionchange = () => database.close()
}
```

#### IndexedDB限制

indexdeDB仍存在跨域的问题，信息不能跨域共享
