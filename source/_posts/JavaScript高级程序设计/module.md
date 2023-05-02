---
title: 模块
date: 2023-05-02 10:43:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 模块

### CommonJS

模块加载为同步操作，模块加载后会被缓存，后续加载会取得缓存的模块

```javascript
let moduleB = require('./moduleB')
module.exports = {
    stuff: moduleB.doStuff()
}
```

### ES6模块

#### 模块标签与定义

```html
<script type='module' src='xxx.js'></script> // 实际行为和defer一致
```

#### 模块行为

- 模块代码只能加载一次，是单例
- 默认在严格模式下执行
- 模块顶级this的值是undefined
- 模块中的var声明不会添加到window对象
- 模块是异步加载和执行的

#### 模块导出

```javascript
// 命名导出
export const foo = 'foo'
const foo = 'foo'
export { foo }
// 默认导出
const foo = 'foo'
export default foo ==> export { foo as default }
```

#### 模块导入

```javascript
// 命名导入
export { foo, bar, baz }
import * as Foo from './foo.js'
// 默认导入
import foo from './foo.js' ==> import { default as foo } from './foo.js'
// 命名和默认导入
import foo , { baz, bar } from './foo.js'
import { default as foo, baz, bar } from './foo.js'
import foo, * as Foo from './foo.js'

```

#### 向后兼容

```html
// 支持模块的会执行，不支持的不会执行
<script type='module' src='module.js'></script>
// 不支持模块的执行，支持的不执行
<script nomodule src='module.js'></script>
```
