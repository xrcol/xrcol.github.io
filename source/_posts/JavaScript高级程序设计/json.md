---
title: JSON
date: 2023-04-30 12:06:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## JSON

### 语法

#### 简单值

字符串（必须双引号）、数值、布尔值和null，特殊值undefined不可以

#### 对象

属性名必须添加双引号

#### 数组

和JavaScript数组字面量形式相同

### 解析与序列化

#### 序列化选项

##### 过滤结果

```javascript
let book = {
    title: 'Professional JavaScript',
    authors: [
        'Nicholas C. Zakas',
        'Matt Frisbie'
    ],
    edition: 4,
    year: 2017
}
let jsonText = JSON.stringify(book, ['title', 'edition']) // 只包含这两个属性

jsonText = JSON.stringify(book, (key, value) => {
    switch(key) {
        case 'authors':
            return value.join(',')
        case 'year':
            return undefined // 返回undefined会忽略该属性
        default:
            return value
    }
})
```

##### 字符串缩进

```javascript
let book = {
    title: 'Professional JavaScript',
    authors: [
        'Nicholas C. Zakas',
        'Matt Frisbie'
    ],
    edition: 4,
    year: 2017
}
let jsonText = JSON.stringify(book, null, 2) // 第三个参数控制缩进和空格
jsonText = JSON.stringify(book, null, '----') // 也可以使用字符来控制缩进
```

##### toJSON方法

```javascript
let book = {
    title: 'Professional JavaScript',
    edition: 4,
    year: 2017,
    toJSON: function() { // 自定义序列化
        return this.year
    }
}
let json = JSON.stringify(book) // 2017
```

### 解析选项

```javascript
let book = {
    title: 'Professional JavaScript',
    edition: 4,
    year: 2017,
    releaseDate: new Date(2023, 4, 30)
}
let text = JSON.stringify(book) // releaseDate会被替换为ISO 8601日期字符串
let bookCopy = JSON.parse(text, (key, value) => key === 'releaseDate' ? new Date(value) : value) // 对日期进行还原
```
