# canvas 入门

## canvas 是什么

- `Canvas` 是 `HTML5` 新增的一个标签，可以理解为是一块"画布"。
- `Canvas` 允许开发者通过 `Javascript` 在这个标签上绘制各种图案，可以将 `Javascript` 理解为是这块画布上的画笔。
- `Canvas` 拥有多种绘制路径、矩形、圆形、字符以及图片的方法。
- `Canvas` 在某些情况下可以 "代替" 图片。
- `Canvas` 可用于动画、游戏、数据可视化、图片编辑器、实时视频处理等领域。

## Canvas 和 SVG 的区别
| `Canvas`| `SVG` |
|--|--|
|用 `Javascript` 动态生成元素（一个 `HTML` 元素）  | 用 `XML` 描述元素（类似 `HTML` 元素那样，可用多个元素来描述一个图形）  |
|位图（缩放失真）|矢量图（缩放不失真）|
|鼠标事件只能通过canvas 接收，其内部图形无法接收|支持鼠标事件，选择方便|
|数据发生变化需要重绘|不需要重绘|
|适合图像数量较大的场景|不适合图像数量较大的场景|
| `IE9` 开始支持, `IE8` 以下可通过引入 `excanvas.js` 支持 | `IE9` 开始支持，`IE8` 以下可通过安装 `Adobe SVG Viewer` 支持|

就上面的描述而言可能有点难懂，你可以打开  `AntV` 旗下的图形编辑引擎做对比。`G6` 是使用 `canvas` 开发的，`X6` 是使用 `svg` 开发的。

我的建议是：如果要展示的数据量比较大，比如一条数据就是一个元素节点，那使用 `canvas` 会比较合适；如果用户操作的交互比较多，而且对清晰度有要求（矢量图），那么使用 `svg` 会比较合适。

## canvas 初体验
**学习前端一定要动手敲代码**，然后看效果展示。

起步阶段会用几句代码说明 `canvas` 如何使用，本例会画一条直线。

### 画条直线
一般来说使用 `canvas` 绘制图形需要经过如下几步:

- 在 `HTML` 中 创建 `canvas` 元素
- 使用 `JS` 获取这个 `canvas` 元素
- 获取绘制上下文
- 使用 `canvas` 相关绘制 `API`  绘制图形

代码示例：
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        /* 为了使canvas元素方便展示,故加了一些简单的样式 */
        #canvas {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            border: 1px solid red;
        }
    </style>
</head>

<body>
    <!-- 1、在HTML中创建 canvas 元素 -->
    <canvas id="canvas" width="300" height="200"></canvas>
    <script>
        // 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // 4、绘制图形  
        // moveTo 、 lineTo 和 stroke 方法暂时可以不用管，它们的作用是绘制图形，这些方法在后面会讲到~
        ctx.moveTo(100, 100) // 起点坐标 (x, y)
        ctx.lineTo(200, 100) // 终点坐标 (x, y)
        ctx.stroke() // 将起点和终点连接起来
    </script>
</body>

</html>
```

展示效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/35a60daf3be3465b8ce0ff0610c76e60.png)
### 注意事项
#### canvas 有默认宽高
`canvas` 有 默认的 宽度( `300px` ) 和 高度( `150px` )
如果不在 `canvas` 上设置宽高，那 `canvas` 元素的默认宽度是 `300px` ，默认高度是 `150px` 。

#### 设置 canvas 宽高
可通过两种方式设置 `canvas` 宽高
**方式一：**
`canvas` 元素提供了 `width` 和 `height` 两个属性，可设置它的宽高。
需要注意的是，这两个属性只需传入数值，不需要传入单位（比如 `px` 等）。
```html
<canvas id="canvas" width="300" height="200"></canvas>
```
**方式二：**
通过 `JS` 设置宽高
依然只需要传入数值，不需要传入单位
```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        canvas.width = "600";
        canvas.height = "400";
```

**注意：不能通过 CSS 设置画布的宽高**，如果使用 `css` 设置 `canvas` 的宽高，会出现 **内容被拉伸** 的后果！！！

#### 线条默认宽度和颜色
线条的默认宽度是 `1px` ，默认颜色是黑色。
但由于默认情况下 `canvas` 会将线条的中心点和像素的底部对齐，所以会导致显示效果是 `2px` 和非纯黑色问题。

## canvas 中的坐标系
在绘制基础图形之前，需要先搞清除 `canvas` 使用的坐标系。
`canvas` 使用的是 `W3C` 坐标系 ，也就是遵循我们屏幕、报纸的阅读习惯，从上往下，从左往右。

![在这里插入图片描述](https://img-blog.csdnimg.cn/beb76b8b25c0445594f1c34849bedd6e.png#pic_center)


## 基础图形
### 直线
#### 一条直线
最简单的起步方式是画一条直线。
需要用到这3个方法：
- `moveTo(x1, y1)`  可以理解为移动到某一个点，并将这个点作为起点
- `lineTo(x2, y2)` 下一个点的坐标 
- `stroke()` 将所有坐标用一条线连起来

```javascript
  // 绘制直线
  ctx.moveTo(50, 100) // 起点坐标
  ctx.lineTo(200, 50) // 下一个点的坐标
  ctx.stroke() // 将上面的坐标用一条线连接起来
```

上面的代码所呈现的效果，可以看下图解释

![在这里插入图片描述](https://img-blog.csdnimg.cn/9eb287a091a845b6a58320cb150ca9fe.png#pic_center)
#### 多条直线
如需画多条直线，可以重复用会上面那几个方法。

```javascript
		// 第一条直线
        ctx.moveTo(100, 100) // 起点坐标 (x, y)
        ctx.lineTo(200, 100) // 终点坐标 (x, y)
        ctx.stroke() // 将起点和终点连接起来
        // 第二条直线
        ctx.moveTo(50,60);
        ctx.lineTo(150,60);
        ctx.stroke();
```

呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb3d67e4ee0544b7aeed7e083bd9c09d.png)
#### 设置样式
- `lineWidth`：线的粗细
- `strokeStyle`：线的颜色
- `lineCap`：线帽：默认: `butt`; 圆形: `round`; 方形:`square`

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // 4、绘制图形
        ctx.moveTo(100, 100) // 起点坐标 (x, y)
        ctx.lineTo(200, 100) // 终点坐标 (x, y)
        ctx.stroke() // 将起点和终点连接起来
        // 修改直线的宽度
        ctx.lineWidth = 8;
        // 修改直线的颜色
        ctx.strokeStyle = "blue";
        // 修改直线两端样式
        ctx.lineCap = "round";

        ctx.stroke();
```

呈现效果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/5ad19d2899914f8d9c2d376243137c38.png)

#### 新开路径
在绘制多条线段的同时，还要设置线段样式，通常需要开辟新路径，要不然样式之间会相互污染。

比如这样

```javascript
	// 2、获取canvas元素
    const canvas = document.getElementById("canvas");
    // 3、获取绘制上下文
    const ctx = canvas.getContext("2d");
    // 4、绘制图形
    ctx.moveTo(100, 100) // 起点坐标 (x, y)
    ctx.lineTo(200, 100) // 终点坐标 (x, y)
    ctx.stroke() // 将起点和终点连接起来
    // 修改直线的宽度
    ctx.lineWidth = 8;
    // 修改直线的颜色
    ctx.strokeStyle = "blue";
    // 修改直线两端样式
    ctx.lineCap = "round";

    ctx.stroke();

    // 修改直线的颜色
    // ctx.strokeStyle = "red"; // 如果加上这行代码,那么两条直线的颜色都是红色

    // 第二条直线
    ctx.moveTo(50,60);
    ctx.lineTo(150,60);
    ctx.stroke();
```
呈现效果如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20cec1cd9e904b24822df7fabe3411ec.png)
如果不想相互污染，需要做2件事：

- 使用 `beginPath()` 方法，重新开一个路径
- 设置新线段的样式（必须项）

在设置 `beginPath()` 的同时，也**各自设置样式**。这样就能做到相互不影响了。

### 折线
和 直线 差不多，都是使用 `moveTo()` 、`lineTo()` 和 `stroke()` 方法绘制。
折线可以理解为是多个点连接起来,故只要设置起点和后续点位，最后连接起来即可。

代码展示：

```javascript
	// 2、获取canvas元素
    const canvas = document.getElementById("canvas");
    // 3、获取绘制上下文
    const ctx = canvas.getContext("2d");
    // 4、绘制图形
    ctx.moveTo(50, 100) // 起点坐标 (x, y)
    ctx.lineTo(100, 200) // 终点坐标 (x, y)
    ctx.lineTo(200, 250) // 终点坐标 (x, y)
    ctx.lineTo(250,100)
    // 修改直线的宽度
    ctx.lineWidth = 1;
    // 修改直线的颜色
    ctx.strokeStyle = "blue";

    ctx.stroke();
```
效果展示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/9e6b6342ae284c5db292762365ec90b7.png)
画这种折线，最好在草稿纸上画一个坐标系，自己计算并描绘一下每个点大概在什么什么位置，最后在 `canvas` 中看看效果。

### 矩形
根据前面的基础，我们可以 使用线段来描绘矩形，但 `canvas` 也提供了 `rect()` 等方法可以直接生成矩形。

#### 使用线段描绘矩形
可以使用前面画线段的方法来绘制矩形

```javascript
	// 2、获取canvas元素
    const canvas = document.getElementById("canvas");
    // 3、获取绘制上下文
    const ctx = canvas.getContext("2d");
    // 绘制矩形
    ctx.moveTo(50, 50);
    ctx.lineTo(200, 50);
    ctx.lineTo(200, 120);
    ctx.lineTo(50, 120);
    ctx.lineTo(50, 50); // 需要闭合
    // 使用 closePath() 方法进行闭合，推荐使用 closePath()
    // ctx.closePath()
    
    ctx.strokeStyle = "blue";
    ctx.stroke();
```
#### 使用 `strokeRect()` 描边矩形
-  `strokeStyle`：设置描边的属性（颜色、渐变、图案）
-  `strokeRect(x, y, width, height)`：描边矩形（ `x` 和 `y` 是矩形左上角起点；`width` 和 `height` 是矩形的宽高）
- `strokeStyle` 必须写在 `strokeRect()` 前面，不然样式不生效。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // 绘制矩形
        ctx.strokeStyle = "blue";
        ctx.strokeRect(50, 50, 100, 200);
```
呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/144717a0338d48d5b5b2343082385a91.png)
#### 使用 `fillRect()` 填充矩形
`fillRect()` 和 `strokeRect()` 方法差不多，但 `fillRect()` 的作用是填充。

`fillStyle` 必须写在 `fillRect()` 之前，不然样式不生效。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // 填充矩形
        ctx.fillStyle = "green";
        ctx.fillRect(50,50,150,100);
```
呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/218eca5ddf354c768d013d53218709f8.png)

#### 同时使用 `strokeRect()` 和 `fillRect()`
同时使用 `strokeRect()` 和 `fillRect()` 会产生描边和填充的效果

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.strokeStyle = "green";
        ctx.strokeRect(50, 50, 200, 100); 
        ctx.fillStyle = "yellow";
        ctx.fillRect(50, 50, 200, 100);
```
呈现效果如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/b85cc5f32c2c427a83742bc7a12d035d.png)
#### 使用 `rect()` 生成矩形
`rect()` 和 `fillRect()` 、`strokeRect()` 的用法差不多，唯一的区别是：
 `strokeRect()` 和 `fillRect()` 这两个方法调用后会**立即绘制**；`rect()` 方法被调用后，不会立刻绘制矩形，而是需要**调用 `stroke()` 或 `fill()` 辅助渲染**。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.strokeStyle = "green";
        ctx.fillStyle = "yellow";

        ctx.rect(50, 50, 200, 100);

        ctx.stroke();
        ctx.fill();
```
实现的效果和刚刚一样

#### 使用 clearRect() 清空矩形
使用 `clearRect()` 方法可以清空指定区域。

```javascript
clearRect(x, y, width, height)
```
其语法和创建 `ctx.rect()` 差不多。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.fillStyle = "yellow";
        ctx.rect(50, 50, 200, 100);
        // 填充矩形
        ctx.fill();
        // 清空矩形
        ctx.clearRect(100, 75, 100, 50);
```
呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/9d9be6068fbd4cdb97a0bcb2cbc856ae.png)
#### 清空画布
`canvas` 画布元素是矩形，所以可以通过下面的代码把整个画布清空掉。

```javascript
// 省略部分代码
ctx.clearRect(0, 0, cnv.width, cnv.height)
```
要清空的区域：从画布左上角开始，直到画布的宽和画布的高为止。

### 多边形
`Canvas` 要画多边形，需要使用 `moveTo()` 、 `lineTo()` 和 `closePath()` 。

#### 三角形
虽然三角形是常见图形，但 `canvas` 并没有提供类似 `rect()` 的方法来绘制三角形。
需要确定三角形3个点的坐标位置，然后使用 `stroke()` 或者 `fill()` 方法生成三角形。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.moveTo(50,50);
        ctx.lineTo(200,50);
        ctx.lineTo(200,200);
        ctx.closePath();
        ctx.strokeStyle = "blue";

        ctx.stroke();
```

呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/5d62929acd2e459d9e1928f8ad848c51.png)
#### 菱形
有一组邻边相等的平行四边形是菱形

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.moveTo(150, 50);
        ctx.lineTo(250, 100);
        ctx.lineTo(150, 150);
        ctx.lineTo(50, 100);
        ctx.closePath();
        ctx.strokeStyle = "blue";

        ctx.stroke();
```

呈现效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/c775a0ebc64340a780617f72e3078640.png)
要绘制直线类型的图形，在草稿纸上标记出起始点和每个拐角的点，然后再连线即可。相对曲线图形来说，直线图形是比较容易的。

### 圆形
绘制圆形的方法是 `arc()`。

```javascript
// x,y 圆心坐标  r 半径  sAngle 开始角度  eAngle  结束角度  
// counterclockwise  绘制方向 true-逆时针  false-顺时针  默认为 false
arc(x, y, r, sAngle, eAngle，counterclockwise)
```

开始角度和结束角度，都是以弧度为单位。例如  `180°` 就写成  `Math.PI`  ，`360°` 写成  `Math.PI * 2`  ，以此类推。
在实际开发中，为了让自己或者别的开发者更容易看懂弧度的数值， `1°` 应该写成  `Math.PI / 180`。

**注意：绘制圆形之前，必须先调用 `beginPath() `方法！！！ 在绘制完成之后，还需要调用 `closePath()` 方法！！！**

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.strokeStyle = "blue";
        ctx.beginPath();
        ctx.arc(150, 150, 80, 0, 360 * Math.PI / 180);
        ctx.closePath();

        ctx.stroke();
```

呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4aaa6b07be9a45eca9d53d87dd8b92a4.png)
### 半圆
使用 `arc()` 方法画圆时，没做到刚好绕完一周（`360°`）就直接闭合路径，就会出现半圆的状态。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.strokeStyle = "blue";
        ctx.beginPath();
        // 顺时针画半圆  如果希望半圆弧在上面(即逆时针画半圆)则再传一个参数为true即可
        ctx.arc(150, 150, 100, 0, 180 * Math.PI / 180);
        ctx.closePath();

        ctx.stroke();
```

呈现效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/913000bdfb75445a8f9940ade8b3212e.png)
### 弧线
使用 `arc()` 方法画半圆时，如果最后不调用 `closePath()` 方法，就不会出现闭合路径。也就是说，那是一条弧线。
在 `canvas` 中，画弧线有2种方法：`arc()` 和 `arcTo()` 。

####  `arc()`画弧线
如果想画一条 `0° ~ 30°` 的弧线，可以这样写

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.strokeStyle = "blue";
        ctx.beginPath();
        ctx.arc(150, 150, 100, 0, 30 * Math.PI / 180); 
        // ctx.closePath();

        ctx.stroke();
```
呈现效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/a2af552bb5f6446fb5c5a64ce8878609.png)
#### `arcTo()` 画弧线
`arcTo()` 的使用方法会更加复杂，如果初学看不太懂的话可以先跳过，看完后面的再回来补补。

```javascript
// cx: 两切线交点的横坐标   cy: 两切线交点的纵坐标
// x2: 结束点的横坐标  y2: 结束点的纵坐标   radius: 半径
arcTo(cx, cy, x2, y2, radius)
```
其中，`(cx, cy)` 也叫控制点，`(x2, y2)` 也叫结束点。
是不是有点奇怪，为什么没有 `x1` 和 `y1` ？
`(x1, y1)` 是开始点，通常是由 `moveTo()` 或者 `lineTo()` 提供。
`arcTo()` 方法**利用 开始点、控制点和结束点形成的夹角，绘制一段与夹角的两边相切并且半径为 `radius` 的圆弧**。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.strokeStyle = "blue";
        ctx.moveTo(40,40);
        ctx.arcTo(120, 40, 120, 120, 80); 
        ctx.stroke();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/44d66e8534174dc1933de1e379303f26.png)
## 基础样式
前面学完基础图形，接下来可以开始了解一下如何设置元素的基础样式。
### `stroke()` 描边
用法：

```javascript
		// 1、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 2、获取绘制上下文
        const ctx = canvas.getContext("2d");
		ctx.stroke();
```

前面的案例中，其实已经知道使用 `stroke()` 方法进行描边了。这里就不再多讲这个方法。

### `lineWidth` 线条宽度
`lineWidth` 默认值是 `1` ，默认单位是 `px`

用法：

```javascript
		// 1、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 2、获取绘制上下文
        const ctx = canvas.getContext("2d");
		ctx.lineWidth = 2;
```

###  `strokeStyle` 线条颜色 
使用 `strokeStyle` 可以设置线条颜色
用法：

```javascript
		// 1、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 2、获取绘制上下文
        const ctx = canvas.getContext("2d");
		ctx.strokeStyle = "blue";
```
### `lineCap` 线帽
线帽指的是线段的开始和结尾处的样式，使用 `lineCap` 可以设置

用法：

```javascript
		// 1、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 2、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // butt 默认值  无线帽
        // square 方形线帽
        // round 原型线帽
		ctx.lineCap= "round";
```

使用 `square` 和 `round` 的话，**会使线条变得稍微长一点点**，这是给线条增加线帽的部分，这个长度在日常开发中需要注意。

线帽只对线条的开始和结尾处产生作用，对拐角不会产生任何作用。

### `lineJoin` 拐角样式
如果需要设置拐角样式，可以使用 `lineJoin` 。

用法：

```javascript
		// 1、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 2、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // miter  默认值  尖角
        // bevel  斜角
        // round  圆角
		ctx.lineJoin= "round";
```

### `setLineDash()` 虚线
使用 `setLineDash()` 方法可以将描边设置成虚线。

用法：

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // 第一条直线，蓝色
        ctx.strokeStyle = "blue";
        ctx.moveTo(40, 40);
        ctx.lineTo(240, 40);
        // 只传一个值，实线与空白都是4px
        ctx.setLineDash([4])
        ctx.stroke();

        ctx.beginPath();
        // 第二条直线，红色
        ctx.strokeStyle = "red";
        ctx.moveTo(40, 80);
        ctx.lineTo(240, 80);
        // 传两个值，实线是4px，空白是8px
        ctx.setLineDash([4, 8])
        ctx.stroke();

        ctx.beginPath();
        // 第三条直线，黄色
        ctx.strokeStyle = "green";
        ctx.moveTo(40, 120);
        ctx.lineTo(240, 120);
        // 传3个及以上的参数,实线与空白依次为 4px 8px 2px 4px 8px ...
        ctx.setLineDash([4, 8, 2])
        ctx.stroke();
```
呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/95a12582b2514cf7a2cafeedaf9e7404.png)
此外，还可以始终 `ctx.getLineDash()` 获取虚线不重复的距离；

用 `ctx.lineDashOffset` 设置虚线的偏移位。

### 填充
使用 `fill()` 可以填充图形，根据前面的例子应该掌握了如何使用 `fill()`

可以使用 `fillStyle` 设置填充颜色，默认是黑色。

### 非零环绕填充
在使用 `fill()` 方法填充时，需要注意一个规则：非零环绕填充。

在使用 `moveTo` 和 `lineTo` 描述图形时，**如果是按顺时针绘制，计数器会加`1`；如果是逆时针，计数器会减`1`。**

**当图形所处的位置，计数器的结果为0时，它就不会被填充。**

举个栗子

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        // 外层矩形
        ctx.moveTo(50, 50);
        ctx.lineTo(250, 50);
        ctx.lineTo(250, 250);
        ctx.lineTo(50, 250);
        ctx.closePath();

        // 内层矩形
        ctx.moveTo(200, 100);
        ctx.lineTo(100, 100);
        ctx.lineTo(100, 200);
        ctx.lineTo(200, 200);
        ctx.closePath();
        ctx.fillStyle = "blue";
        ctx.fill();
```

呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/92371e93cd384b15a93e50fc62f988eb.png)
请看看上面的代码，我画了`2`个矩形，它们都没有用 `beginPath()` 方法开辟新路径。

内层矩形是逆时针绘制的，所以内层的值是 `-1` ，它又经过外层矩形，而外层矩形是顺时针绘制，所以经过外层时值 `+1`，最终内层的值为 `0` ，所以不会被填充。

## 文本
`canvas` 提供了一些操作文本的方法。

为了方便演示，我们先了解一下在 `canvas` 中如何给本文设置样式。

### `font` 样式
和 `CSS` 设置 `font` 差不多，`canvas` 也可以通过 `font` 设置样式。

用法：

```javascript
ctx.font = "font-style font-variant font-weight font-size/line-height font-family"
```

如果需要设置字号 `font-size`，同时设置 `font-family`。

```javascript
ctx.font = "60px 宋体";
```

### `strokeText()` 描边
可以使用 `strokeText()` 方法进行文本描边

用法：

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.font = "60px 宋体";
        // 可使用 strokeStyle 设置描边颜色
        // ctx.strokeStyle = "blue";
        // text 要绘制的内容
		// x   横坐标，文本左边要对齐的坐标（默认左对齐）
		// y   纵坐标，文本底边要对齐的坐标
		// maxWidth   可选参数，表示文本渲染的最大宽度（px），如果文本超出 maxWidth 设置的值，文本会被压缩。
        ctx.strokeText("Hello World", 200, 150);
```

呈现效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/5d8b25788c5844c5bd5bf9a1a9359313.png)
### `fillText()` 填充
使用 `fillText()` 可填充文本，用法和 `strokeText()` 一样。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.font = "60px 宋体";
        ctx.fillStyle = "blue";
        ctx.fillText("Hello World", 200, 150);
```

呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/cbe8d11fd021491eb280a2eeae1ff264.png)
### `measureText()`  获取文本长度
`measureText().width` 方法可以获取文本的长度，单位是 `px` 。

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        ctx.font = "60px 宋体";
        ctx.fillStyle = "blue";
        const text = "Hello World"
        ctx.fillText(text, 200, 150);

        console.log(ctx.measureText(text).width)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/cda208028ebd4f5599f5700886cefb3b.png)
### `textAlign` 水平对齐方式
使用 `textAlign` 属性可以设置文字的水平对齐方式，一共有`5`个值可选

- `start`: 默认。在指定位置的横坐标开始。
- `end`: 在指定坐标的横坐标结束。
- `left`: 左对齐。
- `right`: 右对齐。
- `center`: 居中对齐。

在大多数情况下, `start`  和 `left`  的效果好像是一样的，`end` 和 `right` 也好像是一样的;但在某些国家或者某些场合，阅读文字的习惯是 从右往左 时，`start` 就和 `right` 一样了，`end` 和 `left` 也一样。

### `textBaseline` 垂直对齐方式
使用 `textBaseline` 属性可以设置文字的垂直对齐方式。

在使用 `textBaseline` 前，需要自行了解 `css` 的文本基线。

用一张网图解释一下基线
![](https://img-blog.csdnimg.cn/img_convert/7293d3fd5f1530efd0d666427fcdec70.webp?x-oss-process=image/format,png)
`textBaseline` 可选属性：

- `alphabetic`: 默认。文本基线是普通的字母基线。
- `top`: 文本基线是 `em` 方框的顶端。
- `bottom`: 文本基线是 `em` 方框的底端。
- `middle`: 文本基线是 `em` 方框的正中。
- `hanging`: 文本基线是悬挂基线。

**注意：在绘制文字的时候，默认是以文字的左下角作为参考点进行绘制**

## 图片
在 `canvas` 中可以使用 `drawImage()` 方法绘制图片。

### 渲染图片
渲染图片的方式有 `2` 中，一种是在 `JS` 里加载图片再渲染，另一种是把 `DOM` 里的图片拿到  `canvas` 里渲染。

渲染的语法：

```javascript
// image  要渲染的图片对象
// (dx,dy)  图片左上角坐标
drawImage(image, dx, dy)
```

#### `JS` 版
在 `JS` 里加载图片并渲染，有以下几个步骤：

- 创建 `Image` 对象
- 引入图片
- 等待图片加载完成
- 使用 `drawImage()` 方法渲染图片

```javascript
		// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        canvas.width = 600;
        canvas.height = 500;
        // 创建图Image对象
        const image = new Image();
        // 引入图片
        image.src = "https://tse3-mm.cn.bing.net/th/id/OIP-C.UmMTgQ8rtt4nprFXf8ZcYwHaKd?w=182&h=257&c=7&r=0&o=5&dpr=1.3&pid=1.7";
        // 等待图片加载
        image.onload = () => {
            // 绘制图片
            ctx.drawImage(image, 60, 60);
        }
```

呈现效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/30da714db21047b695c538d9b2a768d7.png)
#### `DOM`版

```html
<!-- 1、在HTML中创建 canvas 元素 -->
    <canvas id="canvas"></canvas>
    <img src="https://tse3-mm.cn.bing.net/th/id/OIP-C.UmMTgQ8rtt4nprFXf8ZcYwHaKd?w=182&h=257&c=7&r=0&o=5&dpr=1.3&pid=1.7"
        alt="image" id="image" />
    <script>
        // 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        canvas.width = 600;
        canvas.height = 500;
        const image = document.getElementById("image");
        window.onload = () => {
            // 绘制图片
            ctx.drawImage(image, 60, 60);
        }
    </script>
```
呈现效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/b66954ca485b4d24bafe9e29b215a6cd.png)
因为图片是从 `DOM` 里获取到的，所以一般来说，只要在 `window.onload` 这个生命周期内使用 `drawImage` 都可以正常渲染图片。

### 设置图片宽高
前面的例子都是直接加载图片，图片默认的宽高是多少就加载多少。
如果需要指定图片宽高，可以在前面的基础上再添加两个参数：

```javascript
// dw dh 指定绘制图片的宽高
drawImage(image, dx, dy, dw, dh)
```

```javascript
// 2、获取canvas元素
        const canvas = document.getElementById("canvas");
        // 3、获取绘制上下文
        const ctx = canvas.getContext("2d");
        canvas.width = 600;
        canvas.height = 500;
        // 创建图Image对象
        const image = new Image();
        // 引入图片
        image.src = "https://tse3-mm.cn.bing.net/th/id/OIP-C.UmMTgQ8rtt4nprFXf8ZcYwHaKd?w=182&h=257&c=7&r=0&o=5&dpr=1.3&pid=1.7";
        // 等待图片加载
        image.onload = () => {
            // 绘制图片
            ctx.drawImage(image, 60, 60, 300, 300 * 334 / 236);
        }
```

呈现效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/8d501fc1c328450eb814589c92e4e14d.png)
### 截取图片
截图图片同样使用 ·drawImage()· 方法，只不过传入的参数数量比之前都多，而且顺序也有点不一样了。

```javascript
//  image  图片对象
//  (sx,sy)  开始截取的坐标
//  sw,sh    截取的宽高
//  (dx,dy)  图片左上角的坐标
//  dw,dh    图片的宽高
drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh)
```

## 总结
本文主要讲解了在 `canvas` 中绘制一些基础图形，还有一些基础样式设置。
