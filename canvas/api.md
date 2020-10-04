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



### 1.5 文本

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
  
  

### 1.6 渐变色和图像填充

- `CanvasRenderingContext2D.createLinearGradient(x0, y0, x1, y1)`：定义线性渐变样式。
- `CanvasRenderingContext2D.createRadialGradient(x0, y0, r0, x1, y1, r1)`：定义辐射渐变样式。
- `CanvasRenderingContext2D.createPattern(image, repetition)`：定义图像填充样式。
  - 平面上重复：repeat;
  - x轴上重复：repeat-x;
  - y轴上重复：repeat-y;
  - 不使用重复：no-repeat;



### 1.7 阴影



## 2 图像处理

### 2.1 图像变换



