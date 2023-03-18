---
title: 接口、lambda表达式和内部类
date: 2023-03-18 15:53:00
tags: Java
categories: Java核心技术
---

## 接口、lambda表达式和内部类

### 接口

#### 概念

接口中的所有方法默认属于public，在声明方法时，不需要提供关键字public

#### 特性

- 接口不是类，不能使用new运算符实例化
- 可以声明接口的变量，同时变量必须引用一个实现了接口的类对象
- 可以使用instanceof检测一个对象是否实现了某个接口
- 接口中定义变量，默认为public static final

#### 静态方法

java8后，可以在接口中增加静态方法

#### 默认方法

```java
public interface Comparable<T> {
    default int compareTo(T other) {
        return 0;
    }
}
```

主要用于“接口演化”，如果给接口添加了一个非默认方法，则必须修改接口实现类；如果是默认方法，则不需要修改实现类，同时在实现类调用新增加的方法，将自动调用接口中的默认实现

#### 默认方法冲突

- 超类优先：一个类扩展了一个超类，同时实现了一个接口，并从超类和接口继承了相同的方法
- 接口冲突：一个类实现了两个接口，接口中存在同名的默认方法，则必须在实现类中实现该方法，来解决冲突

### lambda表达式

```java
(String first, String second) -> first.length() - second.length();
// 如果可以推断出参数类型，则可以忽略类型
Comparator<String> cmp = (first, second) -> first.length() - second.length();
// 如果没有参数，仍要提供空括号
() -> { for (int i = 100; i >= 0; i--) System.out.println(i);};
```

如果lambda表达式只在某些分支返回一个值，而其它分支不返回值，是不合法的

#### 方法引用

object::instanceMethod  -  System.out::println等价于x -> System.out.println(x)

Class::staticMethod  -  Math::pow等价于(x, y) -> Math.pow(x, y)

Class::instanceMethod  -  String::compareToIgnoreCase等价于(x, y) -> x.compareToIgnoreCase(y)

### 内部类

```java
class TalkingClock {
    private boolean beep;
    public void start(int interval, final boolean beep) {
        ActionListener listener = new TimePrinter(this);
        Timer t = new Timer(interval, listener);
        t.start();
    }
    class TimePrinter implements ActionListener {
        private TalkingClock outer;
        public TimePrinter(TalkingClock outer) {
            this.outer = outer;
        }
        public void actionPerformed(ActionEvent e) {
            if (outer.beep) {
                System.out.println("beep");
            }
        }
    }
}

```

这种情况下编译后，会在**TalkingClock**添加静态方法access$000,对于**if(outer.beep)**的访问会变为**if(TalkingClock.access$000)**

```java
public void start() {
    class TimePrinter implements ActionListener {
        public void actionPerformed(ActionEvent e) {
            if (beep) {
                System.out.println("beep");
            }
        }
    }
}
```

这种情况下编译后，在生成的**TalkingClock$1TimePrinter**中会存在**val$beep**变量，在调用构造函数时，会传入**beep**，并存储在**val$beep**中，之后则访问局部变量

```java
final int[] counter = new int[1];
for (int i = 0; i < dates.length; i++) {
    dates[i] = new Date() {
        public int compareTo(Date other) {
            counter[0]++;
            return super.compareTo(other);
        }
    }
}
```

如果想更新一个在封闭作用域中的值，可以采用数组的方式，因为普通的变量必须是final，无法进行更改

#### 匿名内部类

```java
public void start(int interval, int beep) {
    ActionListener listener = new ActionListener() {
        public void actionPerformed(ActionEvent e) {
            if (beep) {
                System.out.println("beep");
            }
        }
    };
    Timer t = new Timer(interval, listener);
    t.start();
    // 或者使用Lambda的方式
    Timer t = new Timer(interval, event -> {
        if (beep) {
            System.out.println("beep");
        }
    });
    t.start();
}
```

对于静态方法，如果需要包含当前类的类名，则应该使用如下表达式

```java
new Object(){}.getClass().getEnclosingClass() // gets class of static method
```

#### 静态内部类

静态内部类的对象没有对外围类对象的引用特权，其它和普通的内部类一致

### 代理

#### 使用方式

```java
class TraceHandler implements InvocationHandler {
    private Object target;
    public TraceHandler(Object t) {
        target = t;
    }
    public Object invoke(Object proxy, Method m, Object[] args) {
        return m.invoke(target, args);
    }
}
Object value = "test";
InvocationHandler handler = new TraceHandler(value);
Class[] interfaces = new Class[] { Comparable.class };
Object proxy = Proxy.nenProxyInstance(null, interfaces, handler);
```

#### 代理类特性

- 所有的代理类都覆盖了Object类中的toString，equals和hashCode方法
- 对于特定的类加载器和接口数组调用两次newProxyInstance方法，只能得到同一个类的两个对象 **Class proxyClass = Proxy.getProxyClass(null, interfaces)**
- 代理类一定是public和final的
