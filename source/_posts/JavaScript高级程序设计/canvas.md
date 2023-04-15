---
title: 动画与canvas图形
date: 2023-04-15 15:28:00
tags: JavaScript
categories: JavaScript高级程序设计
---

## 动画与canvas图形

### requestAnimationFrame

```javascript
function rafThrottle(fn) {
    let lock = false
    return function(...args) {
        if (lock) return
        lock = true
        window.requestAnimationFrame((time) => {
            fn.apply(this, args)
            lock = false
        })
    }
}

let requestID = window.requestAnimationFrame(() => {
    console.log('repaint')
})
window.cancelAnimationFrame(requestID)
```

### 基本的画布功能

```javascript
// 将canvas转为图片显示
let drawing = document.getElementById('drawing')
let imgURL = drawing.toDataURL('image/png')
let image = document.createElement('img')
image.src = imgURL
document.body.appendChild(image)
```

### 2D绘图上下文

#### 填充和描边

```javascript
let drawing = document.getElementById('drawing')
let context = drawing.getContext('2d')
context.strokeStyle = 'red'
context.fillStyle = '#0000ff' // 可以是字符串表示颜色、图案对象和渐变对象
```

#### 绘制矩形

```javascript
context.fillStyle = '#0000ff'
context.fillRect(10, 10, 50, 50) // 填充矩形
context.strokeStyle = '#ff0000'
context.strokeRect(30, 30, 50, 50) // 描边矩形框

context.clearRect(20, 20, 10, 10) // 擦除一个矩形区域
```

#### 绘制路径

必须首先调用context.beginPath()表示开始绘制新路径

```javascript
context.beginPath()
context.arc(x, y, radius, startAngle, endAngle, counterclockwise) // 绘制弧线，最后一个参数表示是否逆时针计算起始角度和结束角度（默认顺时针）
context.arcTo(x1, y1, x2, y2, radius) // 以给定半径，经由(x1, y1)绘制一条从上一点到(x2, y2)的弧线
context.bezierCurveTo(c1x, c1y, c2x, c2y, x, y) // 以(c1x, c1y)和(c2x, c2y)为控制点，绘制一条从上一点到(x, y)的弧线（三次贝塞尔曲线）
context.quadraticCurveTo(cx, cy, x, y) // 以(cx, cy)为控制点，绘制一条从上一点到(x, y)的弧线（二次贝塞尔曲线）
context.lineTo(x, y) // 绘制一条从上一点到(x, y)的直线
context.moveTo(x, y) // 不绘制线条，只把绘制光标移动到(x, y)
context.rect(x, y, width, height) // 以给定宽度和高度绘制一个矩形，创建的是一条路径，不是图形
```

创建路径之后，可以调用closePath()方法绘制一条返回起点的线，或者调用fill()、stroke()方法来画路径

#### 绘制文本

```javascript
context.font = 'bold 14px Arial' // css指定的字体样式
context.textAlign = 'start' // 文本的对齐方式
context.textBaseLine = 'top' // 指定文本的基线
context.fillText('22', 100, 20) // 渲染文本，模拟网页中渲染
context.strokeText('33', 200, 20) // 描画文本，中间空心
context.measureText('helloworld') // 度量文本宽度
```

#### 变换

```javascript
context.rotate(angle) // 围绕原点把图像旋转angle弧度
context.scale(scaleX, scaleY) // 通过在x轴乘以scaleX、在y轴乘以scaleY来缩放图像
context.translate(x, y) // 把原点移动到(x, y)
context.transform(m1_1, m1_2, m2_1, m2_2, dx, dy) // 通过矩阵乘法直接修改矩阵
context.setTransform(m1_1, m1_2, m2_1, m2_2, dx, dy) // 把矩阵重置为默认值，再以传入的参数调用transform()

context.save() // 保存应用到绘图上下文的设置和变换
context.restore() // 恢复之前的绘图上下文设置
```

#### 绘制图像

```javascript
let image = document.image[0]
context.drawImage(image, dx, dy)
context.drawImage(image, dx, dy, dWidth, dHeight)
context.drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)
```

#### 阴影

```javascript
context.shadowColor = 'rgba(0, 0, 0, 0.5)' // css颜色值，表示要绘制的阴影颜色
context.shadowOffsetX = 5 // 阴影相对于形状或路径的x坐标的偏移量
context.shadowOffsetY = 5 // 阴影相对于形状或路径的y坐标的偏移量
context.shadowBlur = 5 // 像素，表示阴影的模糊量
```

#### 渐变

```javascript
let gradient = context.createLinearGradient(30, 30, 70, 70)
gradient.addColorStop(0, 'white')
gradient.addColorStop(1, 'black')
context.fillStyle = gradient
context.fillRect(30, 30, 50, 50)

gradient = context.createRadialGradient(55, 55, 10, 55, 55, 20) // 圆柱体的两个表面
```

#### 图案

```javascript
let image = document.images[0]
pattern = context.createPattern(image, 'repeat') // 也可以接受video元素或者canvas元素
context.fillStyle = pattern
context.fillRect(10, 10, 150, 150)
```

#### 图像数据

```javascript
let imageData = context.getImageData(10, 5, 50, 150)
console.log(imageData.width)
console.log(imageData.height)
console.log(imageData.data)
// 每个像素在data数组中都由4个值表示，分别代表红、绿、蓝和透明度值
red =  data[0]
green = data[1]
blue = data[2]
alpha = data[3]
context.putImageData(imageData, dx, dy)
```

#### 合成

```javascript
// 修改全局透明度
context.globalAlpha = 0.5
// 新绘制的形状如何与上下文已有的形状融合
context.globalCompositionOperation = 'source-over'
```
