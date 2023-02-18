---
title: servlet-basic
date: 2023-02-09 21:49:34
tags: Java
categories: Java-Web-开发详解阅读笔记
---

## Java-Web-基础😀

### 部署Java程序到Tomcat

#### 静态部署🎈

直接将编译后的项目文件夹或者war包放到webapps中

#### 动态部署🎈

```xml
<Context path="/test" docBase="project path" reloadable="true" />
```

- 直接将这插入到server.xml中host节点中
- %tomcat_home%/conf/enginename/hostname/，在这创建对应项目名的project-name.xml，并将上面内容拷贝到xml中(⚠️项目的上下文路径将以project-name为准，与Context节点的path无关)

### Servlet请求匹配规则

采用最长匹配，如果没有找到匹配的则调用容器默认的servlet处理，没有配置默认的servlet，则发送HTTP404消息
