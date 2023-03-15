---
title: 继承
date: 2023-03-15 22:41:00
tags: Java
categories: Java核心技术
---

## 继承

### 类、超类和子类

#### 子类构造器

如果子类的构造器没有显示地调用超类的构造器，则将自动调用超类默认（没有参数）的构造器

#### 多态

静态绑定：private方法、static方法、final方法或者构造器，编译器可以准确知道调用哪个方法

动态绑定：调用的方法依赖于隐式参数的实际类型，调用方法时需要进行搜索，时间开销比较大

#### 阻止继承

定义类或方法时，使用final，来阻止类被继承，阻止方法被覆盖

#### 访问修饰符

|   private    |    public    |      protected       |    默认    |
| :----------: | :----------: | :------------------: | :--------: |
| 仅对本类可见 | 对所有类可见 | 对本包和所有子类可见 | 对本包可见 |

#### equals方法

```java
public class Employee {
    public boolean equals(Employee otherObject) {
        if (this == otherObject) return true;
        if (otherObject == null) return false;
        if (getClass() != otherObject.getClass()) return false;
        Employee other = (Employee) otherObject;
        return Objects.equals(field, other.field);
    }
}
```

#### hashCode方法

```java
public int hashCode() {
    // Objects.hashCode
    return Objects.hash(name, salary);
}
```

### 枚举类

```java
public enum Size {
    SMALL("S"), MEDIUM("M")
}
Size s = Enum.valueOf(Size.class, "SMALL");
Size[] values = Size.values();
Size.MEDINUM.ordinal() // enum枚举中常量的位置  1
```

### 反射

#### Class类

```java
try {
    Class cls = Class.forName("java.util.Random");
    Object obj = cls.newInstance();
} catch (Exception e) {

}
```

#### getFields和getDeclaredFields

```java
class Parent {
    public int publicField;
    protected int protectedField;
    private int privateField;
}

class Child extends Parent {
    public int publicField2;
    protected int protectedField2;
    private int privateField2;
}

public class TestMain2 {
    public static void main(String[] args) {
        Field[] fields = Child.class.getFields();
        fields[0].setAccessible(true); //
        Child.class.getDeclaredMethod()
        System.out.println("getFields(): " + Arrays.toString(fields));
        // getFields(): [public int Child.publicField2, public int Parent.publicField]
        fields = Child.class.getDeclaredFields();
        System.out.println("getDeclaredFields(): " + Arrays.toString(fields));
        // getDeclaredFields(): [public int Child.publicField2, protected int Child.protectedField2, private int Child.privateField2]
        fields = Child.class.getSuperclass().getDeclaredFields();
        System.out.println("parent getDeclaredFields(): " + Arrays.toString(fields));
        // parent getDeclaredFields(): [public int Parent.publicField, protected int Parent.protectedField, private int Parent.privateField]
    }
}
```

#### 反射调用方法

```java
Method m1 = Employee.class.getMethod("getName");
String name = (String) m1.invoke(herry); // 调用实例方法
String name2 = (String) m1.invoke(null); // 调用静态方法
```
