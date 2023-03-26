---
title: 泛型程序设计
date: 2023-03-26 15:36:00
tags: Java
categories: Java核心技术
---

## 泛型程序设计

### 泛型类

```java
public class Pair<T> {
    private T first;
    private T second;

    public T getFrist() {
        return first;
    }
    public setFrist(T newValue) {
        first = newValue;
    }
}
```

### 泛型方法

普通类中定义泛型方法，需要在方法定义的返回值前面加上类型变量

```java
class ArrayAlg {
    public static <T> T getMiddle(T a) {
        return a;
    }
}
```

### 类型变量的限定

如果使用类作为限定，则必须是限定列表中的第一个

```java
public static <T extends Comparable> T getMiddle(T a) {
    return a;
}
```

### 泛型代码和虚拟机

#### 类型擦除

原始类型用第一个限定的类型变量来替换，如果没有给定限定符则用Object替换

#### 翻译泛型方法

```java
class DateInerval extends Pair<LocalDate> {
    public void setSecond(LocalDate second) {

    }
}
// 在类型擦除后变为
class DateInterval extends Pair {
    public void setSecond(LocalDate second) {

    }
}

DateInterval interval = new DateInterval(...);
Pair<LocalDate> pair = interval;
pair.setSecond(pair);
// Pair中只有setSecond(Object)，虚拟机用pair引用的对象调用这个方法，因此将会调用DateInterval.setSecond(Object)
// 此时会生成桥方法
public void setSecond(Object second) {
    setSecond((Date) second);
}
```

### 约束和局限

- 不能用基本类型实例化类型参数，没有```Pair<double>```
- 运行时类型查询只适用于原始类型，```getClass()```返回Pair.class
- 不能创建参数类型化的数组，```Pair<String> table = new Pair<>[10]```
- 不能实例化类型变量，```new T()```
- 不能构造泛型数组，```T[] mm = new T[2]```

### 通配符类型

#### 通配符

子类限定：? extends Employee

超类限定: ? super Manager

无限定：?
