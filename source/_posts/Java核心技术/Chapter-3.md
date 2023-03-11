---
title: Java的基本程序设计结构
date: 2023-03-11 14:13:00
tags: Java
categories: Java核心技术
---

## Java的基本程序设计结构

### 数据类型

|   类型   | byte  | short |  int  | long  | float | double | char  |
| :------: | :---: | :---: | :---: | :---: | :---: | :----: | :---: |
| 存储大小 | 1字节 | 2字节 | 4字节 | 8字节 | 4字节 | 8字节  | 2字节 |

#### char类型

char类型使用的是**UTF-16**编码，每个字符使用16位表示，通常称为代码单元（code unit），辅助字符采用一对连续的代码单元进行编码，通常称为替代区域（surrogate area）

##### UTF-16

第一个代码级别称为基本的多语言级别，码点从U+0000到U+FFFF，其余的16个级别码点从U+10000到U+10FFFF，约2^20个码位，为了对其进行编码，其做法是将其前10位映射到U+D800-U+DBFF，后10位映射到U+DC00-DFFF。在读取字节时，按照两个字节读取，如果取到的值的范围在代理区，则说明这是一个辅助字符

```java
public static byte[] utf16encode(String str) {
    int[] codePoints = str.codePoints().toArray();
    byte[] bytes = new byte[codePoints.length * 2];
    int index = 0;
    for (int point : codePoints) {
        if (point < 0xFFFF) {
            bytes[index++] = (byte) (point >>> 8);
            bytes[index++] = (byte) (point & 0xFF);
        } else {
            int highSurrogate = ((point - 0x10000) >>> 10) + 0xD800;
            int lowSurrogate = point & 0x3FF;
            bytes[index++] = (byte) (highSurrogate >>> 8);
            bytes[index++] = (byte) (highSurrogate & 0xFF);
            bytes[index++] = (byte) (lowSurrogate >>> 8);
            bytes[index++] = (byte) (lowSurrogate & 0xFF);
        }
    }
    return bytes;
}
```

#### boolean类型

需要看具体的虚拟机实现，确定是占1个字节或者4个字节，对于oracle的Java虚拟机实现，boolean数组被编码为byte数组，每个boolean使用1个字节，单独使用boolean时，被编码为int，占用4个字节

### 运算符

**Math.floorMod**是为了解决有关整数余数问题，比如n % 2，如果n为负数，则余数会为-1。为了解决这个问题，一般要引入一个分支，((n % 12) + 12) % 12

### 大数值

```java
public void bigCalculate() {
    BigInteger a = BigInteger.valueOf(100);
    BigInteger b = BigInteger.valueOf(200);
    BigInteger c = a.add(b);
}
```

### 数组

```java
public void init() {
    // 数组初始化
    int[] a = {1, 2, 3};
    int[] b = new int[] {1, 2, 3};
    a = new int[] {3, 4, 5};
    a = {5, 6, 7} // error
}
```
