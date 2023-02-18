---
title: session-track
date: 2023-02-18 16:55:51
tags: Java
categories: Java-Web-开发详解阅读笔记
---

## 会话跟踪

### Cookie

#### 会话

保存到浏览器内存中，关闭浏览器后，下次访问时，又会创建新的session和sessionId

#### 硬盘

保存到硬盘中，通过Cookie.setMaxAge(num),num为正数

#### 禁用Cookie

当客户端禁用Cookie后，可以通过URL重写机制来跟踪用户会话

```java
// 将sessionID作为请求的一部分
response.encodeURL(path)
response.encodeRedirectURL(path)
```

#### 使用方式

```java
Cookie cookie = new Cookie("userinfo", "test");
cookie.setMaxAge(1800);
response.addCookie(cookie);
Cookie[] cookies = request.getCookies();
```

### HttpSessionBindingListener

当一个对象实现了HttpSessionBindingListener接口，当这个对象被绑定到Session中或者从Session中删除时，Servlet容器会通知这个对象，对象收到通知后，可以做一些其它的操作（在线人数统计）

```java
public class User implements HttpSessionBindingListener {
    public void valueBound(HttpSessionBindingListener event) {
        // 添加到Session中时
    }
    public void valueUnbound(HttpSessionBindingListener event) {
        // 从Session中删除时
    }
}
```
