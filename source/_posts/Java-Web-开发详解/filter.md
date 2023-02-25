---
title: 过滤器的使用和应用场景
date: 2023-02-20 22:04:00
tags: Java
categories: Java-Web-开发详解阅读笔记
---

## Filter使用方式和场景

### 主要接口

Filter: 开发过滤器需要实现该接口，实现init、doFilter和destroy方法

FilterCofig: 在过滤器初始化时，传递信息，调用getInitParameter(name)获取参数的值

FilterChain: 调用过滤器链中的下一个过滤器，如果是最后一个，则调用servlet的service方法

### 过滤器的部署

```xml
<filter>
    <filter-name></filter-name>
    <filter-class></filter-class>
    <init-param>
        <param-name></param-name>
        <param-value></param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name></filter-name>
    <url-pattern></url-pattern>
    <!-- 或者直接指定servlet -->
    <servlet-name></servlet-name>
    <!-- request、include、forward、error -->
    <!--request: 不是requestdispatcher的include和forward时，才触发 -->
    <!--include: requestdispatcher.include调用时，才触发 -->
    <!--forward: requestdispatcher.forward调用时，才触发 -->
    <dispatcher></dispatcher>
</filter-mapping>
```

### 代码示例

```java
public class TestFilter implements Filter {
    public void init(FilterConfig filterConfig) throws ServletException {}
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        chain.doFilter(request, response);
    }
    public void destroy() {}
}
```

### 场景

#### 对请求和响应数据进行替换

通过继承HttpServletRequestWrapper，定义自己的request，并覆盖其中的方法，来实现需要的逻辑，在chain.doFilter时，传递自定义的Request对象

```java
public MyRequestWrapper extends HttpServletRequestWrapper {
    public MyRequestWrapper (HttpServletRequest request) {
        super(request);
    }
    public String getQueryString() {
        String str = "abc=123";
        String originQueryString = super.getQueryString();
        if (null != originQueryString) {
            originQueryString += "&" + str;
            return originQueryString;
        } else {
            return str;
        }
    }
}
```

如果要获取response中的数据，需要自定义response，并且使用ByteArrayOutputStream，让数据写到字节数组中，同时重写HttpServletResponse类的getWriter和getOutputStream方法，返回ByteArrayOutputStream的PrintWriter和ServletOutputStream对象

#### 其它应用场景

对用户请求统一认证

对响应内容压缩，减少传输量

转换图像格式



