# JS 遇到 CPU 密集型代码，耗时长怎么破？ 来试试 Golang+WASM 曲线救国

## 1 写在前面

### 1.1 业务场景

最近遇到一业务需求，为了简述需求，假设业务场景根据全国各地天气气温数据生成气温分布图，需要在 `Web` 前端进行数据动态**插值**操作，大致过程将是已有的全国天气数据根据一种算法生成新的插值数据，然后将插值数据在 `Web` 端利用 `Canvas` 渲染出来。

> ​		**插值**在数值分析的数学领域，插值是一种估计类型，一种在一组离散的已知数据点范围内构造新数据点的方法。在工程学和科学中，通常有许多通过采样或实验获得的数据点，它们代表有限数量的自变量值的函数值。通常需要进行插值，即为自变量的中间值估算该函数的值。
>
> ------  [维基百科](https://en.wikipedia.org/wiki/Interpolation)

根据业务场景，这里插值使用了**克里金法**（Kriging）算法进行插值，如果你不知道**克里金法**算法也没有关系，后文对这个算法有介绍。

**使用 JavaScript 运行这个算法的情况**： 经过2000 条测试数据运行 JavaScript kriging 算法生成结果插值数据，大致会花一分三十秒左右，这也太慢了吧？🤔

完成生成插值数据后，接下来将插值数据进行可视化的方式渲染出来，下图就是原始数据与经过克里金法算法生成的插值数据渲染出来的对比图



![原始数据与生成的插值数据渲染图 - 图片来于[《kriging 插值法–在这一方面的预测很强》](https://www.giserdqy.com/algorithm/36734/)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfdd1npr4j30p0093tis.jpg)

这里 **克里金法**（Kriging）算法不仅可以二维数据进行插值，也可以应用到三维数据上，下图来源于百度百科克里金法词条

![[图片来源于百度百科克里金法词条](https://baike.baidu.com/item/%E5%85%8B%E9%87%8C%E9%87%91%E6%B3%95/5129539)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfe373nt4j30rj0kw0vx.jpg)

### 1.2 什么是 kriging ?

这里主要介绍算法，不涉及算法实现过程及推论，如果你不关心这个算法，也可以跳过这里，不影响后文的理解。

> ​		**克里金法**（Kriging）在统计学中，最初在地统计学中，克里金法或高斯过程回归是一种插值方法，其插值由先验协方差控制的高斯过程建模。在先验的适当假设下，克里金法给出中间值的最佳线性无偏预测。该方法被广泛应用于空间分析和计算机实验。
>
> ------  [维基百科](https://en.wikipedia.org/wiki/Kriging)



克里金算法算法插值操作主要是以下两步：

- 利用已有数据进行数据模型训练
- 根据输入数据预测生成插值数据



`kriging` 算法分类有：

- 普通克里金（Ordinary Kriging, OK）

- 泛克里金（Universal Kriging, UK）

- 协同克里金（Co-Kriging, CK）

- 析取克里金（Disjunctive Kriging, DK）

- 混合算法
  - 回归克里金（regression-Kriging）
  - 神经网络克里金（neural Kriging）
  - 贝叶斯克里金（Bayesian Kriging）

这里算法选取普通克里金（Ordinary Kriging, OK）,下图是来源于维基百科的普通克里金算法理论基础

![Ordinary Kriging - 图片来源于[维基百科](https://en.wikipedia.org/wiki/Kriging)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfdsq0a7oj31c20poteg.jpg)

关于普通克里金算法实现过程及数学公式可查看维基百科 [Ordinary Kriging](https://en.wikipedia.org/wiki/Kriging)，下面是普通克里金的模型函数（半变异函）分类：

- `Spherical` - 球面半变异函数模型。
- `Circular` - 圆半变异函数模型。
- `Exponential` - 指数半变异函数模型。
- `Gaussian` - 高斯（或正态分布）半变异函数模型。
- `Linear` - 采用基台的线性半变异函数模型。



![数学模型的常用形状和方程 - 图片来于[《克里金法的工作原理》](https://pro.arcgis.com/zh-cn/pro-app/tool-reference/3d-analyst/how-kriging-works.htm)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfctwf90sj30da0jtq5g.jpg)

![普通克里金（Ordinary Kriging, OK）的模型函数（半变异函）区别](https://tva1.sinaimg.cn/large/0081Kckwgy1glen8ms8d4j30td0j00xy.jpg)

这里我们暂时选择 `Kriging` 算法函数模型为 `exponential` (指数半变异函数模型)。

### 1.3 kriging 算法实现的开源库

科学计算和数据分析这块还是用 `R` 语言与 `Python` 的人比较多，`Github` 搜索 `kriging` 关键字，关联相关仓库的主要语音是 `R` 与 `Python` 的比较多，其次依次降低的是 `C/C++`、`JavaScript`、`Java`。

就 `Python` 开源实现的 `kriging` 差值算法库有：

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

### 1.4 技术路线考量

这里 `kriging` 算法运行时间的长短与数据量成正相关，`Python` 与 `JavaScript` 本质上都是利用单核资源，算法运行时长应该差不到太多。

如果在浏览器里面进行 `kriging` 算法运行也是可行的，浏览器环境下运行 `kriging` 算法建议使用 [Web Workers](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API) 单独开一个线程跑 `kriging` 算法，避免当前页面窗口 `JavaScript` 引擎线程耗时太长造成 `GUI` 渲染线程阻塞，导致用户操作界面得不到响应，感受到明显的卡顿情况。

> JavaScript 是单线程，GUI 渲染线程与 JS 引擎线程是互斥的，所以如果 JS 执行的时间过长，这样就会造成页面的渲染不连贯，导致用户操作界面得不到响应。

上文提到经过 2000 条测试数据运行 JavaScript kriging 算法生成插值数据，大致会花一分三十秒左右，为什么这么慢呢？因为`kriging` 算法里面需要进行大量的数学函数运算和矩阵运算，故属于 CPU 密集型操作，对于 IO 密集型优势比较强的 Node 相比之下优势就比不明显了，如果采用 `C/C++`、`Rust`、`Go` 这类比较基础的语言编写算法运行速度应该有所提升。

如果算法运行顺序的关联性比较弱的话，那么可以利用多核 CPU 的优势，应该可以再提升一定的速度，但` C/C++`、`Java` 这类语言多线程的操作比较麻烦，恰好笔者了解点 `Golang`，如果利用 `Golang` 的并发设计 `goroutine` 协程机制，来并发利用多核 CPU 的优势编写代码要简单的多，如果后面 `kriging` 算法做成 HTTP 服务的话，也很方便编写并发服务多人同时运行算法进行数据插值。

既然如此，那何不用 `Golang` 重写一下 [kriging.js](https://github.com/oeo4b/kriging.js) 这个代码呢？如果 `Golang` 版  `kriging` 算法运行效率相比于浏览器环境 JavaScript Web Workers 下的算法运行效率比较高的话，那么后面可以做成 **HTTP** 服务的方式提供给前端调用接口返回插值的数据，然后前端根据插值数据渲染出图呢。

不错思路按理可行，如果不考虑返回插值的数据量大的情况及 HTTP 服务的耗时，还不错，但经过运行 JavaScript kriging 算法进行 2000 条数据插值，生成可渲染的矩阵插值数据大致有 6-7 MB，如果这个返回到前端，这还是有点大呢，数据传输耗时还不可以忽略呢。

那么如果把渲染出图的操作也放在服务端呢，最终返回图片格式到前端，这也可行，不过渲染数据出图如果前端定制性要求比较高的话，那么服务端渲染出图操作的代码量比较大。

如果按照上面的技术路线，多用户下服务端运行 `kriging` 算法，如果服务器多核 CPU 的资源用完了，剩下的用户只有等待排队了，这也不太理想了，如果可以让处于排队状态的用户可以选择把 `kriging` 算法运行放到自己电脑上，利用自己电脑 CPU 性能决定生产效率，也行，这样多一个选择也好。

除此之外，还可以试试利用 [WebAssembly](https://developer.mozilla.org/zh-CN/docs/WebAssembly) 技术嘛，将 `Golang` 版重写的 [kriging.js](https://github.com/oeo4b/kriging.js) 放到浏览器环境下运行，将 Go 代码编译成低级的类汇编语言的形式在浏览器里面运行，这或许相比于 `JavaScript` 能提升一定的性能。

> WebAssembly 是一种新的编码方式，可以在现代的网络浏览器中运行 － 它是一种低级的类汇编语言，具有紧凑的二进制格式，可以接近原生的性能运行，并为诸如 C / C ++ 等语言提供一个编译目标，以便它们可以在 Web 上运行。它也被设计为可以与 JavaScript 共存，允许两者一起工作。
>
> ------ [MDN](https://developer.mozilla.org/zh-CN/docs/WebAssembly)



OK，下面就按照以下步骤，进行一一验证上面的思路是否可行

- 编写 Go kriging 算法代码与性能测试和分析
- 利用 WebAssembly 编译 Go 代码与浏览器环境性能测试和分析
- 测试性能对比结果
- 总结一下



## 2 编写 Go kriging 算法代码与性能测试和分析

### 2.1 编写 kriging 代码

普通克里金的模型函数（半变异函）三个模型函数代码

```go
// krigingVariogramGaussian gaussian variogram models
func krigingVariogramGaussian(h, nugget, range_, sill, A float64) float64 {
	return nugget + ((sill-nugget)/range_)*
		(1.0-math.Exp(-(1.0/A)*math.Pow(h/range_, 2)))
}

// krigingVariogramExponential exponential variogram models
func krigingVariogramExponential(h, nugget, range_, sill, A float64) float64 {
	return nugget + ((sill-nugget)/range_)*
		(1.0-math.Exp(-(1.0/A)*(h/range_)))
}

// krigingVariogramSpherical spherical variogram models
func krigingVariogramSpherical(h, nugget, range_, sill, A float64) float64 {
	if h > range_ {
		return nugget + (sill-nugget)/range_
	} else {
		return nugget + ((sill-nugget)/range_)*
			(1.5*(h/range_)-0.5*math.Pow(h/range_, 3))
	}
}
```

根据训练模型预测数据生成插值数据代码

```go
// Predict model prediction
func (variogram *Variogram) Predict(x, y float64) float64 {
	k := make([]float64, variogram.N)
	for i := 0; i < variogram.N; i++ {
		k[i] = variogram.model(
			math.Pow(
				math.Pow(x-variogram.x[i], 2)+math.Pow(y-variogram.y[i], 2),
				0.5,
			),
			variogram.Nugget, variogram.Range,
			variogram.Sill, variogram.A,
		)
	}

	return krigingMatrixMultiply(k, variogram.M, 1, variogram.N, 1)[0]
}
```

代码较多这里只贴出三个模型函数与预测数据代码，更多代码查看 [go-kriging/blob/main/ordinarykriging/ordinarykriging.go](https://github.com/liuvigongzuoshi/go-kriging/blob/main/ordinarykriging/ordinarykriging.go#L5) 

### 2.2 测试 Golang 代码

```go
ordinaryKriging := ordinary.NewOrdinary(data["values"], data["lons"], data["lats"])
// 训练模型
ordinaryKriging.Train(ordinary.Exponential, 0, 100)
// 生成插值后的矩阵数据
gridMatrices := ordinaryKriging.Grid(polygon, 0.01)
// ...
```

#### 2.2.1 调试分析代码耗时

使用 `go tool pprof` 性能监控与分析  `Go` 程序，这里主要调试 CPU 耗时分析，这里 Memory 分析不再展开，`main` 函数加上了下面几行，跑一下代码，生成 cpu_profile 文件。

```go
import "runtime/pprof"

func main() {
  cpuProfile, _ := os.Create("cpu_profile")
  pprof.StartCPUProfile(cpuProfile)
  defer pprof.StopCPUProfile()
  // ...
}
```

输入 `go tool pprof cpu_profile` 命令调试分析刚才生成的 cpu_profile 文件，查看代码 CPU 执行的耗时情况，显示结果如下

```bash
$ go tool pprof cpu_profile
Type: cpu
Time: Dec 5, 2020 at 5:42am (CST)
Duration: 1.72mins, Total samples = 1.57mins (91.64%)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) 
```

程序执行时间 1.72mins，这也太夸张了，比 `JS` 都跑的慢？😐

输入 `top`  命令列出 CPU 占比前十个最高的一些运行结点

 ```bash
(pprof) top
Showing nodes accounting for 80.97s, 85.80% of 94.37s total
Dropped 127 nodes (cum <= 0.47s)
Showing top 10 nodes out of 39
      flat  flat%   sum%        cum   cum%
    25.83s 27.37% 27.37%     29.11s 30.85%  github.com/liuvigongzuoshi/go-kriging/internal/ordinary.matrixSolve
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

matrixSolve 这个方法进行了大量的矩阵运算耗时比较长在情理之中，出人意料的是 math.Exp、math.pow 等标准库的数学方法耗时也较长，为了确认并查看程序执行全部过程，输入 `png` 查看输出报告

![CPU profile](https://tva1.sinaimg.cn/large/0081Kckwgy1gllq4xhyu3j30u00q9q7w.jpg)



`Predict` 函数用到幂运算 `math.pow` 与 `math.Exp` 方法耗时就高达 `15s` 😳

#### 2.2.2 尝试解决 math.Exp、math.pow  函数耗时长的问题

咋个看分析都是 math.Exp、math.pow 这两个包比较耗时，幂运算函数 `func Pow(x, y float64) float64` 参数是浮点数据类型，查看源码发现在计算 `x` 的 `y` 次方计算过程中，需要做一些特殊处理，比较复杂，首先需要对 `x、y` 的值做特殊判断，是否等于 0 与 +-1 及负数的情况做特殊处理，后面浮点数的 `x**y` 运算更是复杂。

一路 Google  查询相关 Golang 内容无果，看到一篇 [Performance of Various Python Exponentiation Methods](https://chrissardegna.com/blog/posts/python-expontentiation-performance/#timing-tests) Python 里面幂运算测试性能的文章，里面提到作者最近在写一个算法来解决一个编码难题，这个问题涉及到在笛卡尔平面上找到一个与所有其他点的距离最小的点，根据勾股定理距离可用函数可以用表达式 `math.sqrt(dx ** 2 + dy ** 2)`。它们可以有几种不同的写法：`dx**2`，`math.pow(dx，2)`和`dx*dx`，有意思的是它们的性能都不相同，以下是测试结果：

| 表达式           | 计时（10万次迭代） |
| :--------------- | :----------------- |
| `x * x`          | 3.87 ms            |
| `x ** 2`         | 80.97 ms           |
| `math.pow(x, 2)` | 83.60 ms           |

最后提到，当幂次超过 15 以及超过 1000 越来越大的时候，math.pow() 与 `x * x` 运行速度也就越来越接近了，文章最后总结 JavaScript 也有这种情况，难到 Go 也有这种情况？🤔

 验证问题，修改 `Predict` 函数，调整 `math.pow(x, 2) ` 为 `x*x`

```go
// Predict model prediction
func (variogram *Variogram) Predict(x, y float64) float64 {
	k := make([]float64, variogram.N)
	for i := 0; i < variogram.N; i++ {
-		k[i] = variogram.model(
-			math.Pow(
-				math.Pow(x-variogram.x[i], 2)+math.Pow(y-variogram.y[i], 2),
-				0.5,
-			),
-			variogram.Nugget, variogram.Range,
-			variogram.Sill, variogram.A,
-		)
+   x_ := x - variogram.x[i]
+		y_ := y - variogram.y[i]
+		h := math.Sqrt(x*x) + y_*y))
+		k[i] = variogram.model(
+			h,
+			variogram.Nugget, variogram.Range,
+			variogram.Sill, variogram.A,
		)
	}

	return krigingMatrixMultiply(k, variogram.M, 1, variogram.N, 1)[0]
}
```

同理修改三个模型函数，对于 `math.Exp(x)` 的耗时处理我们先做一个简单的判断，根据 `e^0` 等于 1 ，同理 `x` 等于 `0`或 `1` 直接返回我们写好的常量。

```go
// krigingVariogramGaussian gaussian variogram models
func krigingVariogramGaussian(h, nugget, range_, sill, A float64) float64 {
+	x := -(1.0 / A) * ((h / range_) * (h / range_))
 	return nugget + ((sill-nugget)/range_)*
-		(1.0-math.Exp(-(1.0/A)*math.Pow(h/range_, 2)))
+		(1.0-exp(x))
 }
 
// krigingVariogramExponential exponential variogram models
func krigingVariogramExponential(h, nugget, range_, sill, A float64) float64 {
+	x := -(1.0 / A) * (h / range_)
 	return nugget + ((sill-nugget)/range_)*
-		(1.0-math.Exp(-(1.0/A)*(h/range_)))
+		(1.0-exp(x))
 }

// krigingVariogramSpherical spherical variogram models
func krigingVariogramSpherical(h, nugget, range_, sill, A float64) float64 {
	if h > range_ {
		return nugget + (sill-nugget)/range_
	} else {
+		x := h / range_
 		return nugget + ((sill-nugget)/range_)*
-			(1.5*(h/range_)-0.5*math.Pow(h/range_, 3))
+			(1.5*(x)-0.5*(x*x*x))
 	}
}
```

写改完代码中类似问题后再跑一次，这次程序耗时如下图所示

![修改后代码后调试生成的 CPU profile](https://tva1.sinaimg.cn/large/0081Kckwgy1gllq5mmvmej30tz11lwl3.jpg)

不错，😏 Interesting！程序运行耗时直接缩短 `48.6%` ，`Predict` 函数从 59.0s 缩短到 17.11s ,math.Exp(x) 从 15.81s 缩短到 9.45s。

剩下来比较耗时的函数就是 `matrixSolve` 与 `math.Exp(x)`，`math.Exp(x)` 这里除了上述的特值判断外目前暂未找到其它优化方法，下面来尝试解决`matrixSolve` 函数耗时长的问题。

#### 2.3.4 尝试优化 `matrixSolve` 函数

`matrixSolve` 函数主要作用是通过高斯-若尔当消元法进行求矩阵的逆，这里求矩阵的逆的主要算法有高斯消元法、LU 分解法，除此之外还有 SVD 分解法与 QR 分解法。高斯消元法的算法有高斯消元法、列选主元的高斯消元、全选主元的高斯消元法、高斯-若尔当消元法，这里用到了高斯-若尔当消元法，时间复杂度也是 `O(n^3)`，占程序总耗时 27-29s  左右。

> [高斯-若尔当消元法 (Gauss-Jordan Elimination) ](https://zh.wikipedia.org/wiki/%E9%AB%98%E6%96%AF-%E8%8B%A5%E7%88%BE%E7%95%B6%E6%B6%88%E5%85%83%E6%B3%95)是[高斯消元法](https://zh.wikipedia.org/wiki/高斯消元法)的另一个版本，相比起高斯消元法，这个算法的效率比较低，却可把方程组的解用矩阵一次过表示出来。
>
> ------  [维基百科](https://zh.wikipedia.org/wiki/%E9%AB%98%E6%96%AF-%E8%8B%A5%E7%88%BE%E7%95%B6%E6%B6%88%E5%85%83%E6%B3%95)

既然高斯-若尔当消元法算法效率比较低又有没有其它可替换的算法呢？在上面几种算法对比了解各种的优缺点及适应的应用场景后，进行测试发现列主元消去法要快一点，但是还是不够快，最后选择专业的数学科学计算 [Gonum](https://github.com/gonum/gonum) 包进行求矩阵的逆, Gonum 里对矩阵的逆运算用到了并发运算，最后 `matrixSolve` 函数只占主协程耗时 1-2s  左右，但使用这个方法（猜测内部用的 LU 分解法）进行求矩阵的逆，与之前的高斯-若尔当消元法相比，最终的插值的数据有有偏差，偏差位在小数点 12 位，在接受范围内，可忽略不计，如下是调用数学科学计算 [Gonum](https://github.com/gonum/gonum) 包的 [Inverse](https://pkg.go.dev/gonum.org/v1/gonum@v0.8.2/mat?readme=expanded#Dense.Inverse) 进行求矩阵的逆代码

```go
func matrixInverse(x []float64, n int) ([]float64, bool) {
	a := mat.NewDense(n, n, x)
	var ia mat.Dense

	err := ia.Inverse(a)
	if err != nil {
		return x, false
	}

	return ia.RawMatrix().Data, true
}
```

关于求矩阵的逆相关有质量的内容：

- 关于高斯消元思想及实现过程可看看[消元法及高斯消元法思想](https://oi-wiki.org/math/gauss/#_1)
- 不同的高斯消元法的性能比较可看看这个[仓库](https://github.com/ecjtuliwei/GaussianElimination) `github.com/ecjtuliwei/GaussianElimination`

#### 2.3.5 发挥 Go 协程的魅力

代码的功能相对比较简单，所以比较容易的定位到了问题的所在，如果还要想进行调优，可以考虑进行并发改造，来发挥 Go 协程的魅力。

`Train` 函数内部算法数据计算关联性比较强，感觉不适合做并发改造，但调用 `Predict` 函数的 `Grid` 函数有做重复事情的味道，这里 `Grid` 函数主要作用根据面数据插值生成网格单元集数据，可以将多次遍历循环生成网格单元数据集的重复逻辑单位分发为多个协程去做这个事情，接下来修改代码，下面是 `Grid` 函数内部改造成并发生成插值数据的主要代码

```go
// ...
var wg sync.WaitGroup
predictCh := make(chan *PredictDate, (b[1]-b[0])*(a[1]-a[0]))
var parallelPredict = func(j, k int, polygon []Point, xTarget, yTarget float64) {
  predictDate := &PredictDate{X: j, Y: k}
  predictDate.Value = variogram.Predict(xTarget,
                                        yTarget,
                                       )
  predictCh <- predictDate
  defer wg.Done()
}

var xTarget, yTarget float64
for j := a[0]; j <= a[1]; j++ {
  xTarget = xlim[0] + float64(j)*width
  for k := b[0]; k <= b[1]; k++ {
    yTarget = ylim[0] + float64(k)*width

    if pipFloat64(currentPolygon, xTarget, yTarget) {
      wg.Add(1)
      go parallelPredict(j, k, currentPolygon, xTarget, yTarget)
    }
  }
}

go func() {
  wg.Wait()
  close(predictCh)
}()

for predictDate := range predictCh {
  if predictDate.Value != 0 {
    j := predictDate.X
    k := predictDate.Y
    A[j][k] = predictDate.Value
  }
}
// ...
```

在尝试并发改造后发现 `Grid` 函数的执行时间从原来的 18s 多降低到 4s 多，不错，还行🤔。

这里多提一下，如果在尝试并发改造后发现改造的结果并不理想，可能是因为使用 channel 进行同步导致阻塞，抵消了多协程带来的性能提升，这种情况就弊大于利了。

经过上面这些优化代码过后，下图是最终优化过后的 CPU profile 分析局部图

![最终优化过后的 CPU profile](https://tva1.sinaimg.cn/large/0081Kckwgy1glm113kwrrj30ub0hbjtu.jpg)



经过基准测试 ，2000 条数据插值生成图片持续运行时间大致 4-6s 左右🚀，不错。这里就不展开赘述将这个工具做成 REST 服务了，OK，可以开始下一步 Go WebAssembly 了。

## 3 利用 WebAssembly 技术编译 Go 代码

### 3.1 编写 Go 代码给 Js 调用方法

主函数方法如下，利用一个通道，让程序一直运行

```go
func main() {
	fmt.Println("Instantiate, kriging WebAssembly! v0.0.5")
	done := make(chan int, 0)
	js.Global().Set("OrdinaryKriging", js.FuncOf(OrdinaryKrigingFunc))
	js.Global().Set("OrdinaryKrigingTrain", js.FuncOf(OrdinaryKrigingTrainFunc))
	<-done
}
```

实现训练模型方法被 JS 调用，代码如下

```go
func OrdinaryKrigingTrainFunc(this js.Value, args []js.Value) interface{} {
	values := make([]float64, args[0].Length())
	for i := 0; i < len(values); i++ {
		values[i] = args[0].Index(i).Float()
	}
	lons := make([]float64, args[1].Length())
	for i := 0; i < len(lons); i++ {
		lons[i] = args[1].Index(i).Float()
	}
	lats := make([]float64, args[2].Length())
	for i := 0; i < len(lats); i++ {
		lats[i] = args[2].Index(i).Float()
	}
	model := args[3].String()
	sigma2 := args[4].Float()
	alpha := args[5].Float()

	variogram := RunOrdinaryKrigingTrain(values, lons, lats, model, sigma2, alpha)
	variogramBuffer, err := json.Marshal(variogram)
	if err != nil {
		log.Fatal(err)
	}

	return string(variogramBuffer)
}

func RunOrdinaryKrigingTrain(values, lons, lats []float64, model string, sigma2 float64, alpha float64) *ordinarykriging.Variogram {
	ordinaryKriging := ordinarykriging.NewOrdinary(values, lons, lats)
	variogram := ordinaryKriging.Train(ordinarykriging.ModelType(model), sigma2, alpha)
	return variogram
}
```

更多代码查看 go kriging wasm [examples](https://github.com/liuvigongzuoshi/go-kriging/blob/d2e356ce63/examples/wasm/main.go) 。

### 3.2 将 Golang 代码编译成 Wasm 文件

```bash
 GOOS=js GOARCH=wasm go build -o kriging.wasm
```

运行上面的命令生成的 wasm 文件都在 3M 以上，官方建议有[两种方案](http://127.0.0.1:5500/examples/kriging-wasm/index.html)，一种通过压缩算法工具进行压缩，另一种使用 [TinyGo](https://github.com/tinygo-org/tinygo) 工具编译生成 Wasm 文件来替换 `go build`。

测试了一下，[TinyGo](https://github.com/tinygo-org/tinygo) 工具编译生成 Wasm 文件是小了很多，只有 392 kb，如果还觉得大还可以使用压缩工具进行压缩，不过遗憾的是 [TinyGo](https://github.com/tinygo-org/tinygo) 目前还不支持多协程 Golang 代码编译成 Wasm 文件。

### 3.3 JavaScript 调用 WebAssembly 主要代码

从 `$(go env GOROOT)/misc/wasm` 目录下拷贝引入 `wasm_exec.js` 文件

```html
<html>
	<head>
		<meta charset="utf-8"/>
		<script src="wasm_exec.js"></script>
		<script>
			const go = new Go();
			WebAssembly.instantiateStreaming(fetch("kriging.wasm"), go.importObject).then((result) => {
				go.run(result.instance);
			});
		</script>
	</head>
	<body></body>
</html>
```

修改  WebAssembly 初始化方法，并添加调用方法，更多代码查看 kriging wasm [example](https://github.com/liuvigongzuoshi/kriging-wasm/blob/main/examples/kriging-wasm/index.js#L137-L162)。

```js
const run = async function (fileUrl) {
  try {
    const file = await fetch(fileUrl);
    const buffer = await file.arrayBuffer();
    const go = new Go();
    const { instance } = await WebAssembly.instantiate(buffer, go.importObject);
    go.run(instance);

    console.time("训练模型耗时");
    const variogram = RunOrdinaryKrigingTrain(
      t,
      x,
      y,
      params.krigingModel,
      params.krigingSigma2,
      params.krigingAlpha,
      params.krigingWidth
    );
    console.timeEnd("训练模型耗时");
    console.log("variogramResult: ", JSON.parse(variogram));

    console.time("训练模型加插值总耗时");
    const gridrResult = RunOrdinaryKriging(
      t,
      x,
      y,
      params.krigingModel,
      params.krigingSigma2,
      params.krigingAlpha,
      params.krigingWidth,
      JSON.stringify(geometry)
    );
    console.timeEnd("训练模型加插值总耗时");
    console.log("gridrResult: ", JSON.parse(gridrResult));
  } catch (err) {
    console.error(err);
  }
};
setTimeout(() => run("./kriging.wasm"));
```

> 如果想了解更多关于Go WASM 查看 [Go WASM Wiki](https://github.com/golang/go/wiki/WebAssembly) 



## 4 测试对比效率 

测试设备 MBP CPU 2.6 GHz 六核Intel Core i7，测试数据 2000+ 条数据，Golang version 1.15.5，Chrome 87，Firefox 83，Kriging 算法函数模型为 spherical (球面半变异函数模型)

| 类型             | JS 版 Chrome 下 | Golang 版 | Golang 协程并发版 | Golang 版编译的 Wams              | Golang 协程并发版编译的 Wams       |
| ---------------- | --------------- | --------- | ----------------- | --------------------------------- | ---------------------------------- |
| 训练模型         | 60-62s          | 2s        | 2s                | 44-50s(Chrome)/130-132s(Firefox)  | 44-50s(Chrome)                     |
| 生成插值矩阵数据 | 59-60s          | 9-10s     | 2-4s              | -                                 | -                                  |
| 总耗时           | 120-122s        | 10-12s    | 4-6s              | 103-106s(Chrome)/61-285s(Firefox) | 122-129s(Chrome)/出现错误(Firefox) |

从上面可以看出，Golang 协程并发版性能是最好的，但 Wams 测试出来的结果就是有点费解了🤔。

首先 Chrome 下 Golang 使用协程版编译的 Wams总体性能倒是跟 JS 版差不多，这就有点迷糊了，从训练模型的耗时来看是要比 JS 代码性能好些，但是生成插值就慢了很多，经过多次测试 Golang 代码编译的 Wams 在浏览器下运行，发现有内存泄露现象，尝试使用 TinyGo 编译生成 Wams 经过测试效果也不是很理想。

这里 Golang 使用协程版编译的 Wams，如果浏览器支持 WebAssembly 多线程，那么就会启用多线程，Chrome 70 以后已经支持 WebAssembly 多线程了。

在 Firefox 下测试 Wams，如果是协程版编译的 Wams 直接就报错了，未使用协程版的耗时比 JS 版的都高，单看训练模型耗时就非常高，排查了一下，发现训练模型函数返回的数据量很大，大概有 200M+，猜测应该是从里面拷数据到 JS 内存的过程中太耗时了，但是经过测试在不返回模型数据的情况下依旧还是这么耗时，编译的同一套代码在 Firefox 与 Chrome 情况各不一样🤔

需要提一下的是上面 JS 版测试未进行 Go 代码那样优化，使用的是 [kriging.js](https://github.com/oeo4b/kriging.js) 这个包直接进行的测试，如果优化的话保守估计应该可提升 10-20s 左右吧，在 JS 里面将算法改成并发版不太容易，只能在 Web Workers 里面再创建 Web Workers 线程，没有经过测试还不确定具体效果怎么样。

## 5 总结

目前 WebAssembly 还不支持调试，只能通过控制台打印相关信息，遇到麻烦很难找到问题出在哪里，不知道是 Golang 编译 Wams 对多进程支持成熟度不够还是头大的 GC 问题。后面有可能的话试一下同样的算法用 Emscripten 编译 C/C++ 的 Wams 看看情况如何。

折腾了这么多还是建议将 kriging 插值算法做成服务，部署到 CPU 比较好的服务器上，其次服务器上最好做一个缓存功能，同样的数据输入就不需要再花时间插值计算一次了。

Golang Kriging 算法包还有继续优化完善的地方，特别是设计到的矩阵运算代码，目前还没有覆盖完整的测试以及 CLI 与使用文档，后面会渐渐添加上，仓库地址：

- [go-kriging](https://github.com/liuvigongzuoshi/go-kriging) - Golang Kriging 算法代码基于 [kriging.js](https://github.com/oeo4b/kriging.js) 重写的，代码与算法上做了优化，并添加了一些新方法。

- [kriging-wasm example](https://github.com/liuvigongzuoshi/kriging-wasm) - go-kriging 算法代码编译的 Wasm 使用情况及测试示例。

- [go-kriging-service](https://github.com/liuvigongzuoshi/go-kriging-service) - 调用 go-kriging 算法包编写的 HTTP 服务，支持多用户并发调用，有简单的日志记录与容错恢复机制功能。

**后话**：kriging 算法耗时主要是矩阵运算这块，倘若应用图形处理器 (GPU) 加速计算这样的算法与基于 CPU 的算法相比较，GPU 加速计算能否取得更快的运算速度呢？

## 参考资料

- [ordinary-kriging](https://ordinary-kriging.surge.sh/) - 普通克里金法在线工具

- [克里金法 (3D Analyst)](https://pro.arcgis.com/zh-cn/pro-app/tool-reference/3d-analyst/kriging.htm) - ArcGIS

- [克里金法的工作原理](https://pro.arcgis.com/zh-cn/pro-app/tool-reference/3d-analyst/how-kriging-works.htm) - ArcGIS

- [克里金法](https://baike.baidu.com/item/%E5%85%8B%E9%87%8C%E9%87%91%E6%B3%95/5129539) - 百科词条

- [Kriging](https://en.wikipedia.org/wiki/Kriging) - wikipedia 克里金法

- [Multivariate_interpolation](https://en.wikipedia.org/wiki/Multivariate_interpolation) - wikipedia 多元插值

- [kriging.js](https://github.com/oeo4b/kriging.js) - Javascript library for geospatial prediction and mapping via ordinary kriging

- [WebAssembly Threads ready to try in Chrome 70](https://developers.google.com/web/updates/2018/10/wasm-threads)

- [c++ - 在浏览器中，多线程WebAssembly的速度比单线程慢，为什么？](https://github.com/bsergeev/MtMergeSort)

- [WebAssembly Interface Types: Interoperate with All the Things!](https://hacks.mozilla.org/2019/08/webassembly-interface-types/)

- [Go, WebAssembly, HTTP requests and Promises](https://withblue.ink/2020/10/03/go-webassembly-http-requests-and-promises.html)

- [golang -- 性能测试和分析](https://www.cnblogs.com/cnblogs-wangzhipeng/p/10153513.html)



文中链接较多建议[原文地址](https://github.com/liuvigongzuoshi/blog/blob/master/README.md#webassembly-%E7%9B%B8%E5%85%B3)查阅。