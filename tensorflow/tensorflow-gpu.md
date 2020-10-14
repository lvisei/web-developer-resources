# Windows 环境搭建 Tensorflow 2.1+ GPU 加速训练支持

## 写在前面

最近准备尝试跑一跑 TensorFlow GPU 训练，平时开发用的 MacBook Pro，无赖显卡是 AMD 只支持 CPU 版，但恰有闲置 Windows 10 笔记本电脑一台显卡型号为 850M。

Tensorflow GPU 训练加速需要支持 CUDA® 的 GPU 显卡，这里提到的 CUDA 指的是，是 NVIDIA 研发的一种并行计算平台和编程模型，它可以通过利用 GPU 的处理能力，可大幅提升计算性能的技术，更多查看[相关内容](https://www.geforce.cn/hardware/technology/cuda/faq)。

## 1 硬件条件

 首先查看自己的显卡型号是否满足条件，官方文档[硬件要求](https://tensorflow.google.cn/install/gpu?hl=zh_cn#hardware_requirements)提到了，CUDA® 架构为 3.5、3.7、5.2、6.0、6.1、7.0 或更高的 NVIDIA® GPU 卡。

### 1.1 查看显卡型号

第一步查看自己的显卡型号，进入 NVIDIA 控制面板，点击系统信息

![QQ20201014-201345](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-201345.png)



显卡型号为 GeForce GTX 850M



![QQ20201014-201520](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-201520.png)



### 1.2 查询显卡是否支持 CUDA®

显卡型号为 [GeForce GTX 850M](http://www.geforce.com/hardware/notebook-gpus/geforce-gtx-850m)，点击查看[支持 CUDA® 的 GPU 卡](https://developer.nvidia.com/cuda-gpus)列表，查看显卡型号是否在支持的列表里面。



![QQ20201014-204817](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-204817.png)



显卡支持 CUDA ，接下来查看 CUDA 驱动版本，NVIDIA 控制面板，系统信息的组件里面



![QQ20201014-211536](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-211536.png)



版本为 11.1.96，在 TensorFlow 支持 CUDA 版本范围上，如果版本过低可查看下面提到的软件下载进行安装最新驱动升级。



## 2 软件下载

### 2.1 下载安装 NVIDIA® GPU 驱动程序

输入信息查询显卡驱动，下载安装最新的 [NVIDIA® GPU 驱动程序](https://www.nvidia.com/drivers)，方便后面支持 CUDA 安装 10.1，CUDA 10.1 需要驱动版本 418.x 或更高版本。



![QQ20201014-213623](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-213623.png)



版本号为 456.71，满足要求，然后下载下来后安装驱动



![QQ20201014-213921](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-213921.png)



### 2.2 下载安装 [CUDA® 工具包](https://developer.nvidia.com/cuda-toolkit-archive) 

CUDA 工具包提供了用于创建高性能GPU加速应用程序的开发环境。TensorFlow（TensorFlow 2.1.0 及更高版本）支持 CUDA® 10.1 的 [CUDA® 工具包](https://developer.nvidia.com/cuda-toolkit-archive) 。



![QQ20201014-231747](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-231747.png)

选择临时文件安装位置，这里选择了默认设置



![QQ20201014-232646](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-232646.png)



然后一直下一步，直到这里，如果电脑上面没有安装 Visual Studio 会提示你安装 Visual Studio，如果后续有打算从源码构建 Tensorflow，则需要安装，具体安装内容可查看 [在 Windows 环境中从源代码构建](https://tensorflow.google.cn/install/source_windows?hl=zh_cn#install_visual_c_build_tools_2019)。



![QQ20201014-234650](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-234650.png)





安装完成之后，会自动添加了几个变量，在终端里面输入 `nvcc -V` 验证安装结果



![QQ20201014-235856](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-235856.png)



### 2.3 下载 cuDNN SDK 7.6

cuDNN 是一个 GPU 加速的深度神经网络库，下载 cuDNN SDK 需要注册 NVIDIA Developer Program，进入下载页面后选择 CUDA 10.1 的 cuDNN 7.6 版本。



![QQ20201015-003912](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201015-003912.png)



下载成功之后将其压缩包解压，拷贝放到 CUDA 目录 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1\`下面，这里拷贝是指将这三个目录下面的文件拷贝到 CUDA 对应的目录里面。



![QQ20201015-012751](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201015-012751.png)



### 2.4 [TensorRT 6.0](https://docs.nvidia.com/deeplearning/sdk/tensorrt-install-guide/index.html) （可选）



用于可缩短用某些模型进行推断的延迟时间并提高吞吐量，可选择 [Zip](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#installing-zip) 方式安装，这里可选安装。

![QQ20201014-224155](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201014-224155.png)



## 3 安装 Anaconda

使用 Anaconda 方便管理包依赖问题。

### 3.1 下载 Anaconda

- 从官网下载：https://www.continuum.io/downloads 不过官网速度比较慢，不太推荐。
- 从清华镜像下载：https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/ 

### 3.2 安装 Anaconda

安装过程也笔记简单，需要注意以下两个地方。



![895802-20200603164956820-683281587](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/895802-20200603164956820-683281587.png)



安装设置



![895802-20200603165015278-991209107](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/895802-20200603165015278-991209107.png)

### 3.3 配置 Anaconda

清华 Anaconda 镜像

https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/



## 4 测试

### 4.1 安装 TensorFlow

创建测试 tensorflow-gpu 虚拟环境

![QQ20201015-022511](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201015-022511.png)

需要注意的是，如果你用 pip 安装官方发布的 TensorFlow ，可以直接安装 tensorflow 包，即 `pip install --upgrade tensorflow`，因为官方对于 TensorFlow 1.x，CPU 和 GPU 软件包是分开的，而 2.x 将其打包在一起的，另外 GPU  软件包也有发布的，你也可以 `pip install --upgrade tensorflow-gpu` 安装 GPU 包。在使用 conda 安装时， TensorFlow 2.x CPU 和 GPU 软件包是分开了的。

使用 conda 安装 tensorflow-gpu，在输入拦输入 tensorflow 选择 tensorflow-gpu 点击 Applly。

![QQ20201015-022937](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201015-022937.png)





### 4.2 运行测试代码

打开当前环境的终端或打开 JupytarLab 或者 Notebook 运行以下代码

```python
import tensorflow as tf
from tensorflow.python.client import device_lib

print(tf.config.list_physical_devices('GPU'))
print(tf.test.gpu_device_name())
print(device_lib.list_local_devices())
```

在 JupytarLab 运行结果为



![QQ20201015-024516](/Users/john/Workspace/web-developer-resources/tensorflow/asserts/QQ20201015-024516.png)



测试通过，安装成功。



### 参考

- [TensorFlow 安装 GPU 支持](https://tensorflow.google.cn/install/gpu?hl=zh_cn)
- [安装 Tensorflow 2.1.0 版本](https://www.pythonf.cn/read/84187)