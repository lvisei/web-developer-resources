# Canvas API

## 1 绘制方法 

### 1.1 路径

- `CanvasRenderingContext2D.beginPath()`：开始绘制路径。
- `CanvasRenderingContext2D.closePath()`：结束路径，返回到当前路径的起始点，会从当前点到起始点绘制一条直线。如果图形已经封闭，或者只有一个点，那么此方法不会产生任何效果。
- `CanvasRenderingContext2D.moveTo(x, y)`：设置路径的起点，即将一个新路径的起始点移动到`(x，y)`坐标。
- `CanvasRenderingContext2D.lineTo(x, y)`：使用直线从当前点连接到`(x, y)`坐标。
- `CanvasRenderingContext2D.fill()`：在路径内部填充颜色（默认为黑色）。
- `CanvasRenderingContext2D.fillStyle`：指定路径填充的颜色和样式（默认为黑色）。
- `CanvasRenderingContext2D.stroke()`：路径线条着色（默认为黑色）。
- `CanvasRenderingContext2D.strokeStyle`：指定路径线条的颜色和样式（默认为黑色）。



### 1.2 线性

- `CanvasRenderingContext2D.lineWidth`：指定线条的宽度，默认为1.0。
- `CanvasRenderingContext2D.lineCap`：指定线条末端的样式，有三个可能的值：`butt`（默认值，末端为矩形）、`round`（末端为圆形）、`square`（末端为突出的矩形，矩形宽度不变，高度为线条宽度的一半）。
- `CanvasRenderingContext2D.lineJoin`：指定线段交点的样式，有三个可能的值：`round`（交点为扇形）、`bevel`（交点为三角形底边）、`miter`（默认值，交点为菱形)。
- `CanvasRenderingContext2D.miterLimit`：指定交点菱形的长度，默认为10。该属性只在`lineJoin`属性的值等于`miter`时有效。
- `CanvasRenderingContext2D.getLineDash()`：返回一个数组，表示虚线里面线段和间距的长度。
- `CanvasRenderingContext2D.setLineDash()`：数组，用于指定虚线里面线段和间距的长度。



### 1.3 矩形

- `CanvasRenderingContext2D.rect(x, y, width, height)`：绘制矩形路径。
- `CanvasRenderingContext2D.fillRect(x, y, width, height)`：填充一个矩形。
- `CanvasRenderingContext2D.strokeRect(x, y, width, height)`：绘制矩形边框。
- `CanvasRenderingContext2D.clearRect(x, y, width, height)`：指定矩形区域的像素



### 1.4 弧形

- `CanvasRenderingContext2D.arc(x, y, radius, startAngle, endAngle, anticlockwise)`：通过指定圆心和半径绘制弧形。
  - `startAngle`、`endAngle`使用的是弧度值。
  - `anticlockwise`表示绘制的方法，是顺时针还是逆时针绘制。它传入布尔值，true表示逆时针绘制，false表示顺时针绘制，缺省值为false。
  
- `CanvasRenderingContext2D.arcTo(x1, y1, x2, y2, radius)`：通过指定两根切线和半径绘制弧形。

- `CanvasRenderingContext2D.ellipse(x, y, radiusX, radiusY, rotation, startAngle, endAngle, anticlockwise)`：绘制椭圆路径。
  - `x` 椭圆圆心的 x 轴坐标，`y` 椭圆圆心的 y 轴坐标。

  - `radius`X 椭圆长轴的半径，`radius`Y 椭圆短轴的半径。

  - `rotation` 椭圆的旋转角度，以弧度表示(**非角度度数**)。

  - `startAngle` 将要绘制的起始点角度，从 x 轴测量，以弧度表示(**非角度度数**)。

  - `endAngle` 椭圆将要绘制的结束点角度，以弧度表示(**非角度度数**)。

  - `anticlockwise` 可选，如果为 `true`，逆时针方向绘制椭圆 （逆时针）， 反之顺时针方向绘制。



### 1.5 曲线

- `CanvasRenderingContext2D.quadraticCurveTo(cpx, cpy, x, y)`：通过一个控制点与一个结束点绘制二次贝塞尔曲线。

  - `cpx` 控制点的 x 轴坐标，cpy` 控制点的 y 轴坐标。
  - `x` 结束点的 x 轴坐标，y` 结束点的 y 轴坐标。
  - 二次贝塞尔曲线[在线转换器](http://tinyurl.com/html5quadratic)。

- CanvasRenderingContext2D.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`：通过两个控制点与一个结束点绘制二次贝塞尔曲线。

  - `cpx1` 第一个控制点的 x 轴坐标，cpy1` 第一个控制点的 y 轴坐标。
  - `cpx1` 第二个控制点的 x 轴坐标，cpy1` 第二个控制点的 y 轴坐标。
  - `x` 结束点的 x 轴坐标，y` 结束点的 y 轴坐标。
  - 三次贝塞尔曲线[在线转换器](http://blogs.sitepointstatic.com/examples/tech/canvas-curves/bezier-curve.html)。

### 1.6 文本

- `CanvasRenderingContext2D.fillText(text, x, y [, maxWidth])`：在指定位置绘制实心字符。

  - `text`：所要填充的字符串。
  - `x`：文字起点的横坐标，单位像素。
  - `y`：文字起点的纵坐标，单位像素。
  - `maxWidth`：文本的最大像素宽度。该参数可选，如果省略，则表示宽度没有限制。如果文本实际长度超过这个参数指定的值，那么浏览器将尝试用较小的字体填充。
- `CanvasRenderingContext2D.strokeText(text, x, y [, maxWidth])`：在指定位置绘制空心字符。
- `CanvasRenderingContext2D.measureText(text)`：返回一个 TextMetrics 对象。
- `CanvasRenderingContext2D.font`：指定字型大小和字体，默认值为`10px sans-serif`。

  - `[font-style] [font-variant] [font-weight] [font-size/line-height] [font-family]`：五个参数均可缺省，各个参数间用逗号隔开
- `CanvasRenderingContext2D.textAlign`：文本的对齐方式，默认值为`start`。
  - `left`：左对齐
  - `right`：右对齐
  - `center`：居中
  - `start`：默认值，起点对齐（从左到右的文本为左对齐，从右到左的文本为右对齐）。
  - `end`：结尾对齐（从左到右的文本为右对齐，从右到左的文本为左对齐）。
- `CanvasRenderingContext2D.direction`：文本的方向，默认值为`inherit`。
  - 默认值为`inherit`，表示继承`<canvas>`或`document`的设置。
  - `ltr` 从左到右。
  - `rtl` 从右到左。
- `CanvasRenderingContext2D.textBaseline`：文本的垂直位置，默认值为`alphabetic`。
  
  - `top`：上部对齐（字母的基线是整体上移）。
  - `hanging`：悬挂对齐（字母的上沿在一根直线上），适用于印度文和藏文。
  - `middle`：中部对齐（字母的中线在一根直线上）。
  - `alphabetic`：默认值，表示字母位于字母表的正常位置（四线格的第三根线）。
  - `ideographic`：下沿对齐（字母的下沿在一根直线上），使用于东亚文字。
  - `bottom`：底部对齐（字母的基线下移）。对于英文字母，这个设置与`ideographic`没有差异。
  
  

### 1.7  渐变色和图像填充

- `CanvasRenderingContext2D.createLinearGradient(x0, y0, x1, y1)`：定义线性渐变样式。
- `CanvasRenderingContext2D.createRadialGradient(x0, y0, r0, x1, y1, r1)`：定义辐射渐变样式。
- `CanvasRenderingContext2D.createPattern(image, repetition)`：定义图像填充样式。
  - 平面上重复：repeat;
  - x轴上重复：repeat-x;
  - y轴上重复：repeat-y;
  - 不使用重复：no-repeat;



### 1.8 阴影

- `CanvasRenderingContext2D.shadowBlur`：阴影的模糊程度，默认为`0`。
- `CanvasRenderingContext2D.shadowColor`：阴影的颜色，默认为`black`。
- `CanvasRenderingContext2D.shadowOffsetX`：阴影的水平位移，默认为`0`。
- `CanvasRenderingContext2D.shadowOffsetY`：阴影的垂直位移，默认为`0`。



### 1.9 合成

- `CanvasRenderingContext2D.globalAlpha`：设置图形和图像透明度的值。默认 `1.0` (不透明)。
- `CanvasRenderingContext2D.globalCompositeOperation`：设置如何在已经存在的位图上绘制图形和图像。



### 1.10 图像

- `CanvasRenderingContext2D.drawImage()`：

```javascript
  ctx.drawImage(image, dx, dy);
  ctx.drawImage(image, dx, dy, dWidth, dHeight);
  ctx.drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight);
```

  - image：绘制到上下文的元素，允许任何的 canvas 图像源([`CanvasImageSource`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/CanvasImageSource))
      - [`CSSImageValue`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/CSSImageValue)
      - [`HTMLImageElement`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/HTMLImageElement)
      - [`SVGImageElement`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/SVGImageElement)
      - [`HTMLVideoElement`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/HTMLVideoElement)
      - [`HTMLCanvasElement`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement)
      - [`ImageBitmap`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/ImageBitmap) 
      - [`OffscreenCanvas`](https://wiki.developer.mozilla.org/zh-CN/docs/Web/API/OffscreenCanvas)
  - sx：可选，图像内部的横坐标，用于映射到画布的放置点上。开始剪切的 x 坐标位置。
  - sy：可选，图像内部的纵坐标，用于映射到画布的放置点上。开始剪切的 y 坐标位置。
  - sWidth：可选，图像在画布上的宽度，会产生缩放效果。如果未指定，则图像不会缩放，按照实际大小占据画布的宽度。被剪切图像的宽度。
  - sHeight：可选，图像在画布上的高度，会产生缩放效果。如果未指定，则图像不会缩放，按照实际大小占据画布的高度。被剪切图像的高度。
  - dx：画布内部的横坐标，用于放置图像的左上角
  - dy：画布内部的纵坐标，用于放置图像的右上角
  - dWidth：可选，图像在画布内部的宽度，会产生缩放效果。
  - dHeight：可选，图像在画布内部的高度，会产生缩放效果。

### 1.11 剪切

`CanvasRenderingContext2D.clip([fillRule])`： 将当前创建的路径设置为当前剪切路径。

`fillRule` 这个算法判断一个点是在路径内还是在路径外。允许的值：

- `nonzero`: [非零环绕原则](http://en.wikipedia.org/wiki/Nonzero-rule)，默认的原则。
- `evenodd`: [奇偶环绕原则](http://en.wikipedia.org/wiki/Even–odd_rule)。

## 2 图像处理

### 2.1 图像变换

- `CanvasRenderingContext2D.rotate(deg)`：图像旋转
- `CanvasRenderingContext2D.scale(sx, sy)`：图像缩放
- `CanvasRenderingContext2D.translate(x, y)`：图像平移
- `CanvasRenderingContext2D.transform(a, b, c, d, e, f)`：通过一个变换矩阵完成图像变换
  - a: 水平缩放，默认值1，单位倍数
  - b: 水平倾斜，默认值0，单位弧度
  - c: 垂直倾斜，默认值0，单位弧度
  - d: 垂直缩放，默认值1，单位倍数
  - e: 水平位移，默认值0，单位像素
  - f: 垂直位移，默认值0，单位像素
- `CanvasRenderingContext2D.setTransform()`：取消前面的图像变换。
- `CanvasRenderingContext2D.currentTransform`：当前的变换矩阵。

### 2.2 像素控制

- `CanvasRenderingContext2D.getImageData(sx, sy, sw, sh)`：将画布读取成一个 ImageData 对象
- `CanvasRenderingContext2D.putImageData(imagedata, dx, dy, [dirtyX], [dirtyY], [dirtyWidth], [dirtyHeight])`：将 ImageData 对象写入画布
- `CanvasRenderingContext2D.createImageData(width, height || imagedata)`：生成 ImageData 对象

### 2.3 路径检测

- `CanvasRenderingContext2D.isPointInPath(x, y, fillRule)`：判断在当前路径中是否包含检测点。
- `CanvasRenderingContext2D.isPointInStroke(x, y)`：检测某点是否在路径的描边线上。



## 3 canvas 状态

### 3.1 `CanvasRenderingContext2D.save()`

使用栈保存当前的绘画样式状态，你可以使用 **restore()** 恢复任何改变。

### 3.2 `CanvasRenderingContext2D.restore()`

恢复到最近的绘制样式状态，此状态是通过 **save()** 保存到”状态栈“中最新的元素。

### 3.3 `CanvasRenderingContext2D.canvas`

`CanvasRenderingContext2D.canvas`属性指向当前`CanvasRenderingContext2D`对象所在的`<canvas>`元素。该属性只读。



## 4 HTMLCanvasElement

### 4.1 `HTMLCanvasElement.getContext(contextType, [contextAttributes])`

返回画布上的绘图上下文。

### 4.2 `HTMLCanvasElement.toDataURL(type, quality)`

- type：字符串，表示图像的格式。默认为`image/png`，另一个可用的值是`image/jpeg`，Chrome 浏览器还可以使用`image/webp`。
- quality：浮点数，0到1之间，表示 JPEG 和 WebP 图像的质量系数，默认值为0.92。

### 4.3 `HTMLCanvasElement.toBlob(callback, mimeType, quality)` 

- callback：回调函数。它接受生成的 Blob 对象作为参数。
- mimeType：字符串，图像的 MIMEType 类型，默认是`image/png`。
- quality：浮点数，0到1之间，表示图像的质量，只对`image/jpeg`和`image/webp`类型的图像有效。