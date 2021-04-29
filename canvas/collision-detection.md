# collision detection

##简单的碰撞检测算法

### 点与多边形

### 多边形与多边形

#### 1、盒模型碰撞

- 矩形与矩形的碰撞(无旋转矩形)
- 圆与圆的碰撞
- 圆与矩形的碰撞
  - 圆形与矩形（无旋转）
  - 圆形与旋转矩形（以矩形中心点为旋转轴）

#### 2、分离轴定理

> 适用于凸多边形

起因：由于盒模型物体的碰撞检测都是基于规则物体（圆形或则矩形），无法试用其他多边形物体

使用原理：假设2个不规则物体有所碰撞，那么必然存在一个方向的投影（可以想想成太阳照射产生的影子）会重合，即若在某一角度光源下，两物体的投影存在间隙，则为不碰撞，否则为发生碰撞

#### 3、像素检测

> 解决凹多边形场景
> 

像素检测原理：检测2个物体在同一像素是否有覆盖，有则碰撞，无则不碰撞

关键函数：getImageData，globalCompositeOperation

使用原理：通过函数计算两个物体在同一个像素点的透明度，然后通过该像素点的实际透明情况来判断是否有碰撞。

#### 4、isPointInPath

> 判断一个点是否在当前路径中

### 参考资源

- [基于canvas的碰撞检测](https://xxyj.github.io/2017/06/07/%E5%9F%BA%E4%BA%8Ecanvas%E7%9A%84%E7%A2%B0%E6%92%9E%E6%A3%80%E6%B5%8B/#)
- [2D collision detection](https://developer.mozilla.org/zh-CN/docs/Games/Techniques/2D_collision_detection)
- [“等一下，我碰！”——常见的2D碰撞检测](https://jelly.jd.com/article/6006b1045b6c6a01506c87dc)
- [多边形碰撞检测](https://zhuanlan.zhihu.com/p/86981378)

