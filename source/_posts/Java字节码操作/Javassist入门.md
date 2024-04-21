---
title: Javassist入门
date: 2024-04-21 11:18:15
tags: Javassist
categories: Java
---

## Javassist入门

### 基础

#### ClassPool

获取一个可以通过javassist修改的类

```java
ClassPool pool = ClassPool.getDefault();
CtClass cc = pool.get("class name");
```

如果找不对对应名称的类，则需要增加classpath或者直接从byte构建类

```java
// 使用某个类的加载路径
pool.insertClassPath(new ClassClassPath(xxx.class));
// 使用文件夹
pool.insertClassPath("/usr/local/javalib");
// 使用URL
ClassPath cp = new URLClassPath("www.xxx.org", 80, "/java/", "org.javassist.");
pool.insertClassPath(cp);
// 使用byte array
byte[] b = a byte array;
String name = class name;
cp.insertClassPath(new ByteArrayClassPath(name, b));
// 基于输入流
InputStream in = an input stream for reading a class file;
CtClass cc = cp.makeClass(in);
```

自定义类实现ClassPath接口，来加载其它资源

```java
public class CustomClassPath implements ClassPath {
    @Override
    public InputStream openClassfile(String s) throws NotFoundException {
        return null;
    }

    @Override
    public URL find(String s) {
        return null;
    }
}
```

卸载加载到classpool中的类

```java
CtClass cc = ...;
cc.detach();
```

#### 修改类结构

修改类名

```java
ClassPool pool = ClassPool.getDefault();
CtClass cc = pool.get("Point");
cc.setName("Pair");
```

冻结类

如果CtClass调用了writeFile或者toBytecode()，则不能再进一步修改类。可以调用getAndRename来重新获取修改

```java
ClassPool pool = ClassPool.getDefault();
CtClass cc = pool.get("Point");
cc.writeFile();
cc.setName("Pair"); // 不能再进行该操作
CtClass cc2 = pool.getAndRename("Point", "Pair");
```

#### 加载修改过的类

CtClass提供了toClass()方法，会调用当前上下文的classloader去加载这个类

```java
ClassPool pool = ClassPool.getDefault();
CtClass cc = pool.get("Point");
cc.setName("Pair");
Class c = cc.toClass();
```

可以提供自己的classloader去加载

```java
Class c = cc.toClass(bean.getClass().getClassLoader());
```

#### 使用javassist.Loader

```java
ClassPool pool = ClassPool.getDefault();
Loader cl = new Loader(pool);
Class c = cl.loaderClass("Pair");
Object pair = c.newInstance();
```

在加载类的时候，再去修改类，需要实现Translator接口

```java
public class MyTranslator implements Translator {
    @Override
    public void start(ClassPool classPool) throws NotFoundException, CannotCompileException {
        // 当javassist.Loader调用addTranslator时触发
    }
     @Override
    public void onLoad(ClassPool classPool, String s) throws NotFoundException, CannotCompileException {
		CtClass cc = classPool.get(classname);
        cc.setModifiers(Modifier.PUBLIC);
    }
}
Translator t = new MyTranslator();
ClassPool pool = ClassPool.getDefault();
Loader cl = new Loader();
cl.addTranslator(pool, t);
```

#### 方法体中插入代码

CtMethod和CtConstructor提供了insertBefore(),insertAfter(),insertAt(),addCatch()方法来修改方法体

如果编译文件时使用了-g，对于insertAt()则可以访问局部变量

```java
class Point {
    int x, y;
    void move(int dx, int dy) {
        x += dx, y += dy;
    }
}
ClassPool pool = ClassPool.getDefault();
CtClass cc = pool.get("Point");
CtMethod m = cc.getDeclaredMethod("move");
m.insertBefore("{System.out.println($1); System.out.println($2);}");
cc.writeFile();
// 修改后的类
class Point {
    int x, y;
    void move(int dx, int dy) {
        {System.out.println(dx); System.out.println(dy);}
        x += dx, y += dy;
    }
}
```

使用addCatch()

```java
CtMethod m = ...;
CtClass etype = ClassPool.getDefault().get("java.io.IOException");
m.addCatch("{ System.out.println($e); throw $e; }", etype);
// 修改后的方法
try {
    // the original method body
}
catch (java.io.IOException e) {
    System.out.println(e);
    throw e;
}
```

#### 添加新方法

```java
CtClass point = ClassPool.getDefault().get("Point");
CtMethod m = CtNewMethod.make("public int xmove(int dx) { x += dx; }", point);
point.addMethod(m);
```

#### 添加字段

```java
CtClass point = ClassPool.getDefault().get("Point");
CtField f = new CtField(CtClass.intType, "z", point);
point.addField(f, "0");    // initial value is 0.
```

#### 读取注解

```java
public @interface Author {
    String name();
    int year();
}
```

注解使用方式如下

```java
@Author(name="Chiba", year=2005)
public class Point {
    int x, y;
}
```

读取注解信息

```java
CtClass cc = ClassPool.getDefault().get("Point");
Object[] all = cc.getAnnotations();
Author a = (Author)all[0];
String name = a.name();
int year = a.year();
System.out.println("name: " + name + ", year: " + year);
```

#### 导入类

```java
ClassPool pool = ClassPool.getDefault();
pool.importPackage("java.awt");
CtClass cc = pool.makeClass("Test");
CtField f = CtField.make("public Point p;", cc); // 这里会把Point当作java.awt.Point
cc.addField(f)
```

### 限制

- 不允许移除方法或者字段，允许修改方法的名字或者访问属性(public, private)
- 不允许修改方法的参数信息，允许添加新的方法，新方法中参数可以自定义