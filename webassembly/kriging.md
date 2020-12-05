# kriging WebAssembly

## 写在前面

### 业务需求

最近遇到一业务需求，为了简述需求假设业务场景根据各地天气气温数据生成气温分布图，需要在 Web 前端进行数据动态**插值**操作，大致过程是将已有数据根据算法生成新的插值数据，然后将生成的插值数据在 Web 端利用Canvas 渲染出来。

> ​		**插值**在数值分析的数学领域，插值是一种估计类型，一种在一组离散的已知数据点范围内构造新数据点的方法。在工程学和科学中，通常有许多通过采样或实验获得的数据点，它们代表有限数量的自变量值的函数值。通常需要进行插值，即为自变量的中间值估算该函数的值。
>
>  ------  [维基百科](https://en.wikipedia.org/wiki/Interpolation)

根据业务场景，这里插值使用了**克里金法（Kriging）**算法进行插值。

完成生成插值数据后，接下来将数据进行可视化的方式渲染出来，下图是插值数据渲染出来的大致结果。

![图片来于《自克里格插值法–在这一方面的预测很强》](https://tva1.sinaimg.cn/large/0081Kckwgy1gldhrf7p2xj30ga0bpaam.jpg)



### 什么是 kriging ?

这里主要介绍算法简介，不涉及算法的实现。

> ​		**克里金法（Kriging）**在统计学中，最初在地统计学中，克里金法或高斯过程回归是一种插值方法，其插值由先验协方差控制的高斯过程建模。在先验的适当假设下，克里金法给出中间值的最佳线性无偏预测。该方法被广泛应用于的域空间分析和计算机实验。
>
> ------  [维基百科](https://en.wikipedia.org/wiki/Kriging)

克里金算法算法插值操作主要以下几步：

- 利用已有数据进行数据模型训练
- 根据输入数据预测生成插值数据

kriging 算法分类

- 普通克里金（Ordinary Kriging, OK）

- 泛克里金（Universal Kriging, UK）

- 协同克里金（Co-Kriging, CK）

- 析取克里金（Disjunctive Kriging, DK）

- 混合算法
  - 回归克里金（regression-Kriging）
  - 神经网络克里金（neural Kriging）
  - 贝叶斯克里金（Bayesian Kriging）

这里算法选取普通克里金，下面是普通克里金（Ordinary Kriging, OK）的模型函数（半变异函）分类

- Spherical - 球面半变异函数模型。
- Circular - 圆半变异函数模型。
- Exponential - 指数半变异函数模型。
- Gaussian - 高斯（或正态分布）半变异函数模型。
- Linear - 采用基台的线性半变异函数模型。

### kriging 算法实现的开源库

数据统计分析这块还是用 R 语言与 Python 的人比较多，Github 搜索 kriging 关键字，关联相关仓库的主要语音是 R 与 Python 的比较多，其次依次降低的是 C++、JavaScript、Java。

就 Python 开源实现的 kriging 差值算法库有

[PyKrige](https://github.com/GeoStat-Framework/PyKrige)

- `OrdinaryKriging`: 2D ordinary kriging with estimated mean
- `UniversalKriging`: 2D universal kriging providing drift terms
- `OrdinaryKriging3D`: 3D ordinary kriging
- `UniversalKriging3D`: 3D universal kriging
- `RegressionKriging`: An implementation of Regression-Kriging

 [GSTools](https://github.com/GeoStat-Framework/GSTools)

- `Simple`：Simple kriging
- `Ordinary`： Ordinary kriging
- `Universal`：Universal kriging
- `ExtDrif`t：External drift kriging (EDK)
- `Detrended`：Detrended simple kriging.

[pyKriging](https://github.com/capaulson/pyKriging)

- `Simple`：Simple kriging
- `RegressionKriging`: An implementation of Regression-Kriging

JavaScript 有一个实现了普通克里金的 [kriging.js](https://github.com/oeo4b/kriging.js) 开源库，实现模型函数有下面三个

- Gaussian: k(**a**,**b**) = w[0] + w[1] * ( 1 - exp{ -( ||**a**-**b**|| / range )2 / A } )
- Exponential: k(**a**,**b**) = w[0] + w[1] * ( 1 - exp{ -( ||**a**-**b**|| / range ) / A } )
- Spherical: k(**a**,**b**) = w[0] + w[1] * ( 1.5 * ( ||**a**-**b**|| / range ) - 0.5 * ( ||**a**-**b**|| / range )3 )

### 技术路线考量

这里 kriging 算法运行时间的长短与数据量成正相关，Python 与 JavaScript 本质上都是利用单核资源，算法运行时长差不多。

如果在浏览器下面进行 kriging 算法运行也是可行的，浏览器环境下运行 kriging 算法建议使用 [Web Workers](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API) 单独开一个线程进行 kriging 算法运行，避免当前页面窗口 JavaScript 引擎线程耗时太长造成 GUI 渲染线程阻塞，导致用户操作界面得不到响应，感受到明显的卡顿情况。

> JavaScript 是单线程，GUI渲染线程与JS引擎线程是互斥的，所以如果JS执行的时间过长，这样就会造成页面的渲染不连贯，导致用户操作界面得不到响应。

经过测试数据 2000 条进行 JavaScript kriging 算法运生成插值数据，大致会花一分三十秒左右，这也太慢了吧？

这里 kriging 算法需要进行大量的数学函数和矩阵运算，故属于 CPU 密集型操作，对于 IO 密集型优势比较强的 Node 优势比较弱，如果采用 C/C++、Rust、Go 这类比较基础的语言应该运行速度上有所提升。

如果考虑到算法数据运行顺序的关联性比较适中的话，那么可以利用多核 CPU 的优势，应该可以再提升一定的速度，但 C/C++、Java 这类语言多线程的操作比较麻烦，刚好最近在学习使用 Go 语言，对于 Go 语言的并发设计 goroutine 协程机制，来并发利用多核 CPU 的优势编写代码要简单的多，如果后面 kriging 算法做成 HTTP 服务的话，也很方便多人同时运行 kriging 算法出插值数据。

既然如此，那何不用 Go 语言重写一下 [kriging.js](https://github.com/oeo4b/kriging.js) 这个代码呢？如果 Go 语言 kriging 算法运行效率相比于浏览器 JavaScript Web Workers 环境下的算法运行效率比较高的话，后面可以 **REST** 服务的方式提供给前端调用接口返回插值的数据，然后前端根据插值数据渲染出图呢？

不错思路按理可行，如果不考虑返回插值的数据量大的情况及 HTTP 服务的耗时时间，还不错，经过测试数据 2000 条进行插值，生成前端可渲染的业务插值数据大致有 6-7 MB，这还是有点大呢，数据交互时间耗时不可以忽略。

那么如果把渲染出图的操作也放在服务端呢，返回最终的图片格式到前端，这思路不错也可行，不过渲染数据出图如果前端定制性要求比较高的话，那么服务端渲染出图操作的代码量比较大。

如果经过以上技术路选进行测试，多用户下服务端进行kriging 算法运行，如果服务器多核 CPU 的资源用完了，剩下的用户只有等待排队了，这也不太理想。如果可以让处于排队状态的用户可以选择把 kriging 算法运行放到浏览器，利用用户自己电脑 CPU 单核性能决定效率，这到也行。

那么既然这一路测试下来做了这么多，还有没有其它思路呢？可不可以将 Go 语言重写一下 [kriging.js](https://github.com/oeo4b/kriging.js) 放到浏览器下运行负责出插值数据呢？

可以利用 [WebAssembly](https://developer.mozilla.org/zh-CN/docs/WebAssembly) 技术嘛，将 Go 代码编译成低级的类汇编语言的形式在浏览器里面运行，着或许能提升不少性能。

> WebAssembly是一种新的编码方式，可以在现代的网络浏览器中运行 － 它是一种低级的类汇编语言，具有紧凑的二进制格式，可以接近原生的性能运行，并为诸如C / C ++等语言提供一个编译目标，以便它们可以在Web上运行。它也被设计为可以与JavaScript共存，允许两者一起工作。
>
> ------ [MDN](https://developer.mozilla.org/zh-CN/docs/WebAssembly)

下面内容主要分为，编写 Go kriging 算法代码、利用 WebAssembly 编译 Go kriging 代码浏览器环境运行、测试对比效率



## Golang kriging 代码

### 编写 kriging 代码



### 测试 Golang 代码

```go
ordinaryKriging := ordinary.NewOrdinary(data["values"], data["lons"], data["lats"])
```

#### 调试分析耗时代码

使用 pprof 性能监控与分析  Go 程序，这里主要调试 CPU 分析，Memory 分析不再展开

`main` 函数加上了下面几行，并执行一下程序，生成 cpu_profile 文件。

```go
import "runtime/pprof"

func main() {
  cpuProfile, _ := os.Create("cpu_profile")
  pprof.StartCPUProfile(cpuProfile)
  defer pprof.StopCPUProfile()
  // ...
}
```

输入以 `go tool pprof cpu_profile` 命令调试分析程序 CPU 执行的 Profiling，显示结果如下

```bash
$ go tool pprof cpu_profile
Type: cpu
Time: Dec 5, 2020 at 5:42am (CST)
Duration: 1.72mins, Total samples = 1.57mins (91.64%)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) 
```

程序执行时间 1.72mins，输入 `top`  命令列出 CPU 占比前十个最高的一些运行结点

 ```bash
(pprof) top
Showing nodes accounting for 80.97s, 85.80% of 94.37s total
Dropped 127 nodes (cum <= 0.47s)
Showing top 10 nodes out of 39
      flat  flat%   sum%        cum   cum%
    25.83s 27.37% 27.37%     29.11s 30.85%  github.com/liuvigongzuoshi/go-kriging/internal/ordinary.krigingMatrixSolve
    15.81s 16.75% 44.12%     15.81s 16.75%  math.Exp
    15.10s 16.00% 60.13%     30.03s 31.82%  math.pow
     6.01s  6.37% 66.49%     21.85s 23.15%  github.com/liuvigongzuoshi/go-kriging/internal/ordinary.krigingVariogramExponential
     4.70s  4.98% 71.47%     59.02s 62.54%  github.com/liuvigongzuoshi/go-kriging/internal/ordinary.(*Variogram).Predict
     3.62s  3.84% 75.31%      3.73s  3.95%  math.modf
     3.58s  3.79% 79.10%      5.30s  5.62%  math.ldexp
     2.22s  2.35% 81.46%      2.22s  2.35%  runtime.nanotime1
     2.09s  2.21% 83.67%      3.19s  3.38%  math.frexp
     2.01s  2.13% 85.80%      2.01s  2.13%  math.IsInf (partial-inline)
(pprof) 
 ```

krigingMatrixSolve 这个方法进行了大量的矩阵运算耗时比较长，出人意料的是 math.Exp、math.pow、math.modf 等标准库的数学方法耗时也较长，为了确认并查看程序执行全部过程，输入 `png` 查看输出报告

![profile002](https://tva1.sinaimg.cn/large/0081Kckwgy1gldg6j5ejlj30u01a87f8.jpg)

#### 解决问题

-





## Golang WebAssembly

 [Go WASM Wiki](https://github.com/golang/go/wiki/WebAssembly) 

  Compiling Go to WebAssembly

- [Compiling Go to WebAssembly](https://www.sitepen.com/blog/compiling-go-to-webassembly/)

## 测试效率 

CPU 2.6 GHz 六核Intel Core i7

数据 2292条数据



Python Web Server 48-49s (插值加生成渲染图)

JavaScript 1.7-1.8m

## 参考链接

- [ordinary-kriging](https://ordinary-kriging.surge.sh/) - 普通克里金法在线工具
- [克里金法 (3D Analyst)](https://pro.arcgis.com/zh-cn/pro-app/tool-reference/3d-analyst/kriging.htm) - ArcGIS
- [克里金法的工作原理](https://pro.arcgis.com/zh-cn/pro-app/tool-reference/3d-analyst/how-kriging-works.htm) - ArcGIS
- [克里金法](https://baike.baidu.com/item/%E5%85%8B%E9%87%8C%E9%87%91%E6%B3%95/5129539) - 百科词条
- [Kriging](https://en.wikipedia.org/wiki/Kriging) - wikipedia 克里金法
- [Multivariate_interpolation](https://en.wikipedia.org/wiki/Multivariate_interpolation) - wikipedia 多元插值
- [kriging.js](https://github.com/oeo4b/kriging.js) - Javascript library for geospatial prediction and mapping via ordinary kriging

