# JS 运行 CPU 密集型代码耗时长怎么破？ 尝试使用 Go+WebAssembly 运行时长缩短xx%

## 写在前面

### 业务需求

最近遇到一业务需求，为了简述需求假设业务场景根据各地天气气温数据生成气温分布图，需要在 Web 前端进行数据动态**插值**操作，大致过程是将已有数据根据一种算法生成新的插值数据，然后将生成的插值数据在 Web 端利用Canvas 渲染出来。

> ​		**插值**在数值分析的数学领域，插值是一种估计类型，一种在一组离散的已知数据点范围内构造新数据点的方法。在工程学和科学中，通常有许多通过采样或实验获得的数据点，它们代表有限数量的自变量值的函数值。通常需要进行插值，即为自变量的中间值估算该函数的值。
>
> ------  [维基百科](https://en.wikipedia.org/wiki/Interpolation)

根据业务场景，这里插值使用了**克里金法**（Kriging）算法进行插值。

完成生成插值数据后，接下来将数据进行可视化的方式渲染出来，下图是插值数据渲染出来的大致结果



![原始数据与生成的插值数据渲染图 - 图片来于[《kriging 插值法–在这一方面的预测很强》](https://www.giserdqy.com/algorithm/36734/)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfdd1npr4j30p0093tis.jpg)

这里**克里金法**（Kriging）算法除了可以二维数据插值也可以三维数据进行插值

![[图片来源于百度百科克里金法词条](https://baike.baidu.com/item/%E5%85%8B%E9%87%8C%E9%87%91%E6%B3%95/5129539)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfe373nt4j30rj0kw0vx.jpg)

### 什么是 kriging ?

这里主要介绍算法，不涉及算法实现过程及数学推论。

> ​		**克里金法**（Kriging）在统计学中，最初在地统计学中，克里金法或高斯过程回归是一种插值方法，其插值由先验协方差控制的高斯过程建模。在先验的适当假设下，克里金法给出中间值的最佳线性无偏预测。该方法被广泛应用于空间分析和计算机实验。
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

这里算法选取普通克里金（Ordinary Kriging, OK）,下图是来源于维基百科的普通克里金算法理论基础

![Ordinary Kriging - 图片来源于[维基百科](https://en.wikipedia.org/wiki/Kriging)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfdsq0a7oj31c20poteg.jpg)

关于普通克里金算法实现过程及数学公式可查看维基百科 [Ordinary Kriging](https://en.wikipedia.org/wiki/Kriging)。

下面是普通克里金的模型函数（半变异函）分类：

- Spherical - 球面半变异函数模型。
- Circular - 圆半变异函数模型。
- Exponential - 指数半变异函数模型。
- Gaussian - 高斯（或正态分布）半变异函数模型。
- Linear - 采用基台的线性半变异函数模型。



![数学模型的常用形状和方程 - 图片来于[《克里金法的工作原理》](https://pro.arcgis.com/zh-cn/pro-app/tool-reference/3d-analyst/how-kriging-works.htm)](https://tva1.sinaimg.cn/large/0081Kckwgy1glfctwf90sj30da0jtq5g.jpg)

![普通克里金（Ordinary Kriging, OK）的模型函数（半变异函）区别](https://tva1.sinaimg.cn/large/0081Kckwgy1glen8ms8d4j30td0j00xy.jpg)

这里我们暂时选择 Kriging 算法函数模型为 exponential (指数半变异函数模型)。

### kriging 算法实现的开源库

科学计算和数据分析这块还是用 R 语言与 Python 的人比较多，Github 搜索 kriging 关键字，关联相关仓库的主要语音是 R 与 Python 的比较多，其次依次降低的是 C++、JavaScript、Java。

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

经过测试数据 2000 条进行 JavaScript kriging 算法运行成插值数据，大致会花一分三十秒左右，这也太慢了吧？

这里 kriging 算法需要进行大量的数学函数和矩阵运算，故属于 CPU 密集型操作，对于 IO 密集型优势比较强的 Node 优势比较弱，如果采用 C/C++、Rust、Go 这类比较基础的语言应该运行速度上有所提升。

如果考虑到算法数据运行顺序的关联性比较适中的话，那么可以利用多核 CPU 的优势，应该可以再提升一定的速度，但 C/C++、Java 这类语言多线程的操作比较麻烦，刚好最近在学习使用 Go 语言，对于 Go 语言的并发设计 goroutine 协程机制，来并发利用多核 CPU 的优势编写代码要简单的多，如果后面 kriging 算法做成 HTTP 服务的话，也很方便多人同时运行 kriging 算法出插值数据。

既然如此，那何不用 Go 语言重写一下 [kriging.js](https://github.com/oeo4b/kriging.js) 这个代码呢？如果 Go 语言 kriging 算法运行效率相比于浏览器 JavaScript Web Workers 环境下的算法运行效率比较高的话，后面可以 **REST** 服务的方式提供给前端调用接口返回插值的数据，然后前端根据插值数据渲染出图呢？

不错思路按理可行，如果不考虑返回插值的数据量大的情况及 HTTP 服务的耗时时间，还不错，经过测试数据 2000 条进行插值，生成前端可渲染的业务插值数据大致有 6-7 MB，这还是有点大呢，数据交互时间耗时不可以忽略。

那么如果把渲染出图的操作也放在服务端呢，返回最终的图片格式到前端，这思路不错也可行，不过渲染数据出图如果前端定制性要求比较高的话，那么服务端渲染出图操作的代码量比较大。

如果经过以上技术路选进行测试，多用户下服务端进行kriging 算法运行，如果服务器多核 CPU 的资源用完了，剩下的用户只有等待排队了，这也不太理想。如果可以让处于排队状态的用户可以选择把 kriging 算法运行放到浏览器，利用用户自己电脑 CPU 单核性能决定效率，这到也行。

那么既然这一路测试下来做了这么多，还有没有其它思路呢？可不可以将 Go 语言重写一下 [kriging.js](https://github.com/oeo4b/kriging.js) 放到浏览器下运行负责出插值数据呢？

可以利用 [WebAssembly](https://developer.mozilla.org/zh-CN/docs/WebAssembly) 技术嘛，将 Go 代码编译成低级的类汇编语言的形式在浏览器里面运行，这或许能提升不少性能。

> WebAssembly是一种新的编码方式，可以在现代的网络浏览器中运行 － 它是一种低级的类汇编语言，具有紧凑的二进制格式，可以接近原生的性能运行，并为诸如C / C ++等语言提供一个编译目标，以便它们可以在Web上运行。它也被设计为可以与JavaScript共存，允许两者一起工作。
>
> ------ [MDN](https://developer.mozilla.org/zh-CN/docs/WebAssembly)



下面按照以下内容分步进行展开：

- 编写 Go kriging 算法代码与性能测试和分析
- 利用 WebAssembly 编译 Go 代码与浏览器环境性能测试和分析
- 测试对比测试分别简述
- 总结



## Go kriging 代码

### 编写 kriging 代码

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

代码较多这里只贴出三个模型函数与预测数据代码，更多代码查看 [github.com/liuvigongzuoshi/go-kriging ordinary](https://github.com/liuvigongzuoshi/go-kriging/blob/d2e356ce633c55d6cc0aa046da4e99c4cf35e8de/internal/ordinary/ordinary.go#L5) pakage。

### 测试 Go 代码

```go
ordinaryKriging := ordinary.NewOrdinary(data["values"], data["lons"], data["lats"])
// 训练模型
ordinaryKriging.Train(ordinary.Exponential, 0, 100)
// 生成插值后的网格数据
gridMatrices := ordinaryKriging.Grid(polygon, 0.01)
// ...
```

#### 调试分析代码耗时

使用 pprof 性能监控与分析  Go 程序，这里主要调试 CPU 耗时分析，这里 Memory 分析不再展开，`main` 函数加上了下面几行，并执行一下程序，生成 cpu_profile 文件。

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

程序执行时间 1.72mins，这也太夸张了，比 `JS` 都跑的慢？😐

输入 `top`  命令列出 CPU 占比前十个最高的一些运行结点

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

krigingMatrixSolve 这个方法进行了大量的矩阵运算耗时比较长在情理之中，出人意料的是 math.Exp、math.pow 等标准库的数学方法耗时也较长，为了确认并查看程序执行全部过程，输入 `png` 查看输出报告

![CPU profile](https://tva1.sinaimg.cn/large/0081Kckwgy1gldg6j5ejlj30u01a87f8.jpg)



`Predict` 函数用到幂运算 `math.pow` 与 `math.Exp` 方法耗时就高达 `15s`，excuse me ？😳

#### 尝试发现问题

咋个看分析都是 math.Exp、math.pow 这两个包比较耗时，幂运算函数 `func Pow(x, y float64) float64` 参数是浮点数据类型，查看源码发现在计算`x`的`y`次方计算过程中，需要做一些特殊处理，比较复杂，首先需要对 `x、y` 的值做特殊判断是否等于 0与+-1及负数的特殊计算处理、后面浮点数的 `x**y` 运算更是复杂。

一路 Google  查询相关 Go 语言内容无果，看到一篇 [Performance of Various Python Exponentiation Methods](https://chrissardegna.com/blog/posts/python-expontentiation-performance/#timing-tests) Python 里面幂运算测试性能的文章，里面提到作者最近在写一个算法来解决一个编码难题，这个问题涉及到在笛卡尔平面上找到一个与所有其他点的距离最小的点，在Python中，根据勾股定理距离可用函数可以用表达式`math.sqrt(dx ** 2 + dy ** 2)`。当时可以有几种不同的写法：`dx**2`，`math.pow(dx，2)`和`dx*dx`，有意思的是它们的性能都不相同，以下是测试结果：

| 表达式           | 计时（10万次迭代） |
| :--------------- | :----------------- |
| `x * x`          | 3.87 ms            |
| `x ** 2`         | 80.97 ms           |
| `math.pow(x, 2)` | 83.60 ms           |

当幂次超过 15 以及超过 1000 越来越大的时候，math.pow() 与 `x * x` 运行速度也就越来越接近了，文章最后总结 JavaScript 也有这种情况，excuse me ？难到 Go 也有这种情况？🤔

#### 验证问题

修改 `Predict` 函数，调整 `math.pow(x, 2) ` 为 `x*x`

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

![修改后 CPU profile](https://tva1.sinaimg.cn/large/0081Kckwgy1glfqxsozl9j30tz255gtj.jpg)

不错，😏 Interesting！程序运行耗时直接缩短 `48.6%` ，`Predict` 函数从 59.0s 缩短到 17.11s ,math.Exp(x) 从 15.81s 缩短到 9.45s。

剩下来比较耗时的函数就是 `krigingMatrixSolve` 与 `math.Exp(x)`，`krigingMatrixSolve` 函数主要是进行通过高斯-约旦消元法进行线性方程组的解， 目前尚未找到其它替换的算法，`math.Exp(x)` 这里除了上述的特值判断外暂未找到其它优化方法。

代码的功能相对比较简单，所以比较容易的定位到了问题的所在，如果还要想进行调优，可以考虑进行并发改造，来发挥 Go 协程的特点，在尝试并发改造后发现改造的结果并不理想，因为使用 channel 进行同步导致阻塞，抵消了多协程带来的性能提升。

另外一个思路就是尽量避免 `math.Exp(x)` 指数运算，即将指数型运算转换为其他时间复杂度较低的运算，

~~多协程是利用 Go 本身的特性和 CPU 的多核运算能力。而这一种方法则纯粹是从数学角度上进行优化，并不具有普适性。改造后再进行测试显示结果如下：~~

## Go WebAssembly

### 编写 Go 代码 给 Js 调用方法

主函数方法如下，利用一个通道，让程序一直运行

```go
func main() {
	fmt.Println("Instantiate, kriging WebAssembly!")
	done := make(chan int, 0)
	js.Global().Set("RunOrdinaryKriging", js.FuncOf(RunOrdinaryKrigingFunc))
	js.Global().Set("RunOrdinaryKrigingTrain", js.FuncOf(RunOrdinaryKrigingTrainFunc))
	<-done
}
```

实现训练模型方法调用

```go
func RunOrdinaryKrigingTrainFunc(this js.Value, args []js.Value) interface{} {
	done := make(chan *ordinary.Variogram, 1)
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

	go func() {
		variogram := RunOrdinaryKrigingTrain(values, lons, lats, model, sigma2, alpha)
		done <- variogram
	}()
	
	variogram := <-done
	variogramBuffer, err := json.Marshal(variogram)
	if err != nil {
		log.Fatal(err)
	}

	return string(variogramBuffer)
}

func RunOrdinaryKrigingTrain(values, lons, lats []float64, model string, sigma2 float64, alpha float64) *ordinary.Variogram {
	ordinaryKriging := ordinary.NewOrdinary(values, lons, lats)
	variogram := ordinaryKriging.Train(ordinary.ModelType(model), sigma2, alpha)
	return variogram
}
```

更多代码查看 go-kriging examples [wasm](https://github.com/liuvigongzuoshi/go-kriging/blob/d2e356ce63/examples/wasm/main.go) 。

### 将 Go 代码编译成 wasm 文件

```bash
 GOOS=js GOARCH=wasm go build -o kriging.wasm
```

### JavaScript 调用 WebAssembly 主要代码

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

修改  WebAssembly 初始化方法，并添加调用方法，更多代码查看 example [kriging-wasm](https://github.com/liuvigongzuoshi/kriging-wasm/blob/main/examples/kriging-wasm/index.js#L127-L137)。

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
      params.krigingAlpha
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

> 更多关于了解Go WASM 查看 [Go WASM Wiki](https://github.com/golang/go/wiki/WebAssembly) 。

## 测试对比效率 

测试设备 MBP CPU 2.6 GHz 六核Intel Core i7，测试数据 2000+ 条数据，Golang version 1.15.5，Chrome 87，Kriging 算法函数模型为 spherical (球面半变异函数模型)。

<!--Python Web Server 48-49s (插值加生成渲染图)-->

<!--JavaScript 1.7-1.8m-->

|                      | JS 代码 Chrome 下 | Golang 代码 | Golang 代码编译的 wams Chrome 下 |
| -------------------- | ----------------- | ----------- | -------------------------------- |
| 训练模型             | 60-62s            | 31-32s      | 178s                             |
| 生成插值网格单元数据 | 59-60s            | 9-10s       |                                  |
| 总耗时               | 120-122s          | 41-42s      | 235s                             |

## 总结

Golang 代码编译的 wams 在浏览器下不是最好，但要比 JS 代码性能好些

Golang Kriging 算法包和使用 example 后面会在 Github 贴出来，不过还没有完善好测试与 CLI文档之类的，后面会完善添加上。

## 32参考链接

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

