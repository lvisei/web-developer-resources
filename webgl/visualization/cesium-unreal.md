# 初探 Cesium for Unreal

## 写在前面

### Cesium?

这个没有啥说的，象征性的贴张图片和链接。

- 官网 - [Cesium.com](https://cesium.com/)
- 在线示例功能 - [sandcastle.cesium.com](https://sandcastle.cesium.com/)

![Cesium](https://tva1.sinaimg.cn/large/008eGmZEgy1gp84v33q0xj31ls0u01av.jpg)



### Unreal Engine?

什么是虚幻引擎呢？

> 虚幻引擎是全球最开放、最先进的实时3D创作平台。经过持续的改进，它已经不仅仅是一款殿堂级的游戏引擎，还能为各行各业的专业人士带去无限的创作自由和空前的掌控力。无论是前沿内容、互动体验还是沉浸式虚拟世界，一切尽在虚幻引擎。  
>
> ​	--- 来自[官网](https://www.unrealengine.com/zh-CN/)

如果没有听过虚幻引擎是什么也没有关系，《绝地求生》和《堡垒之夜》总有听说过吧，就是虚幻引擎这玩意做的。虚幻引擎是 Epic Games 公司开发的游戏引擎，关于 Epic Games 的了解可以看看阮老师的这篇文章---《[Epic Games 研究：Fortnite 和游戏行业的未来](https://www.ruanyifeng.com/blog/2020/06/epic-games.html)》。

### Cesium for Unreal ？

Cesium for Unreal 有什么意义呢？

如果你在使用 `Unreal` 开发游戏，现在需要用到真实世界某个大雪山（地形数据）作为游戏场景，那么现在可以使用 [Cesium for Unreal](https://www.unrealengine.com/marketplace/zh-CN/product/87b0d05800a545d49bf858ef3458c4f7) 这个插件， 就可以将 3D 真实世界的地理空间数据加载到 `Unreal` 中使用了，也就是说游戏中虚拟世界它可以来源于我们物理设备采集的真实世界的空间数据，总之以后你在游戏里感觉到的真实世界与虚拟世界之间的界限也就越了越模糊了。

如果以后我们的 VR 游戏和智能穿戴设备发展到较高水平时，将我们真实世界带到数据的虚拟世界中，在家足不出户就可以玩游戏一样旅游看名山大川，在里面社交，去想去的地方旅游，是不是就跟斯皮尔伯格的《头号玩家》一样，人们对混乱和崩溃边缘的现实世界的失望，最后大家会不会望寄托于《绿洲》的虚拟世界呢？

## 在 Unreal Engine 里面使用 Cesium for Unreal 插件

下面是在 Unreal Engine 里面使用 Cesium 的地理空间数据的效果

|                                                              |                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![CesiumforUnrealAlps-1920x1080-f715e99b688236579e7a6c150b83a29d](https://tva1.sinaimg.cn/large/008eGmZEgy1gp872ppz75j31hc0u015m.jpg) | ![CesiumforUnrealDenverCharacter-1920x1080-cec3f71f713066603ebab1762f2a8720](https://tva1.sinaimg.cn/large/008eGmZEgy1gp872zls6kj31hc0u0tmr.jpg) | ![CesiumforUnrealDenverUnionStation-1920x1080-e0caf247b778a4f404bd48fd8f9a2f4e](https://tva1.sinaimg.cn/large/008eGmZEgy1gp8739xxqkj31hc0u07l5.jpg) |
| ![CesiumforUnrealDrone-1920x1080-a799691c0b254da7f6de3bf59a633f10](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873e3iihj31hc0u018x.jpg) | ![CesiumforUnrealDrone2-1920x1080-9f42c3fc1af6e7ae1973f5d622e77a7e](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873gxfqsj31hc0u0tiw.jpg) | ![CesiumforUnrealMelbourne-1920x1080-ead9a464ce01934453c75b7a343682ec](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873m9godj31hc0u01de.jpg) |
| ![CesiumforUnrealMelbournePlane-1920x1080-eb154db558f373847e1c4fa7c38cbe2d](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873xajldj31hc0u0qkk.jpg) | ![CesiumforUnrealCharacter-1920x1080-71feb9a07b10330fba6ff540c4a5f9e5](https://tva1.sinaimg.cn/large/008eGmZEgy1gp874g13bzj31hc0u0tru.jpg) | ![RealityCaptureInt01-1920x1080-a1591035365feb374be20f4b32abc465](https://tva1.sinaimg.cn/large/008eGmZEgy1gp8744s4k5j31hc0u0ap1.jpg) |

官方的两个宣传视频

<iframe src="//player.bilibili.com/player.html?aid=672499827&bvid=BV1LU4y1h7sb&cid=319875565&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>



<iframe src="//player.bilibili.com/player.html?aid=757469821&bvid=BV1W64y1S7i1&cid=319873803&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

在 Unreal Engine 里面操作

|                                                              |                                                              |                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![7BBC42C2F0E4EC15761D76D319AF984B](https://tva1.sinaimg.cn/large/008eGmZEgy1gp87ux5fp7j31f20u0dlq.jpg) | ![img](https://cesium.com/docs/images/tutorials/cesium-for-unreal-photogrammetry/melbourne-upright.jpg) | ![img](https://images.prismic.io/cesium/27307e53-46f2-4e54-bbe1-6e02bc75626e_unreal-announcement-2.png?auto=compress%2Cformat&w=944) | ![img](https://cesium.com/docs/images/tutorials/cesium-for-unreal-placing/cone-in-italy.jpg) |



官方给的两篇教程，很简单按到步骤来弄就是了，不过对电脑的配置要求很高，不然流程走不下去。

- [Cesium for Unreal Quickstart](https://cesium.com/docs/tutorials/cesium-unreal-quickstart/)
- [Cesium for Unreal Getting Started Tutorials](https://cesium.com/docs/tutorials/cesium-unreal-020-photogrammetry/)

要提一下的是虽然虚幻引擎是跨平台的，支持 linux 和 Mac OSX 版本下载安装，但目前 Cesium for Unreal 插件只支持 Windows 平台。

![Cesium for Unreal plugin](https://tva1.sinaimg.cn/large/008eGmZEgy1gp85v8tti1j31ls0u01kx.jpg)

## Cesium for Unreal 的技术点

### 关于插件技的术路线

都知道目前 `Cesium` 是基于 `WebGl` 建设的，也就是 `JS` 写的 3D 渲染引擎，能在支持 `WebGl` 的浏览器环境中运行，而 `Unreal` 是基于 `C++` 开发的游戏开发引擎，那如何让 `Cesium` 生态里面的数据源能在 `Unreal` 里面使用的呢？下图是 Cesium for Unreal 的架构图。



![unreal-architecture](https://tva1.sinaimg.cn/large/008eGmZEgy1gp87xfmj21j31g50u010o.jpg)

`Cesium` 是专门为 `Web` 构建的开源 3D 引擎，而 [Cesium for Unreal](https://github.com/CesiumGS/cesium-unreal) 是 `Cesium` 的补充，它是基于 `C++` 编写的新开源 [Cesium Native](https://github.com/CesiumGS/cesium-native) 库，在此基础之上为引擎提供 3D Tiles 流与轻量级 glTF 加载支持，最后以高精度的方式渲染出来。

官方也正在并行推进这两个引擎，交叉利用各自的能力，后面也有可能利用 `WebAssembly` 技术来完善和扩展 `Cesium` 在 `Web` 里的功能。


### 支持的数据格式

`Cesium for Unreal` 能支持加载 [cesium ion](https://cesium.com/ion/) 上的所有数据类型，比如 Terrain、Imagery、3D Tiles（Photogrammetry、3D Models、3D Buildings、Point Clouds），但数据的坐标系目前只能是 `WGS84` 的。

当你加载 3D Buildings 时，如果加上去有偏移也支持可以在面板中调整。

![Origin](https://cesium.com/docs/images/tutorials/cesium-unreal-quickstart/cesium-georeference.png)

## 写在后面

最后分享一段 Cesium 作者 Patrick 在 [Cesium for Unreal Now Available](https://cesium.com/blog/2021/03/30/cesium-for-unreal-now-available/) 中提到关于未来的展望。

> We live in an exciting time of ubiquitous sensors capturing the real world; drones and autonomous vehicles carrying sensors; photogrammetry and AI algorithms creating semantic 3D models; data explosion in resolution and collection frequency; plentiful cloud compute and storage; AR and VR devices coming of age; 5G bandwidth on the rise; GPUs that rival past supercomputers; and cell phones that rival yesteryear’s laptops. My prediction is that the dividing line between the physical and digital worlds will continue to blur, as will the line between 3D geospatial and game assets and ecosystems. This ecosystem of ecosystems will be enabled by open standards and platforms of platforms. 3D Tiles may very well become the spatial temporal index for all of it. I won’t be a graphics programmer in geospatial; I will just be a programmer.
>
> One prediction is certain: you will create the future; our role is just to facilitate it.
>
>   --- You and The Future

## 参考资料

- [First Look at RealityCapture Photogrammetry in Cesium for Unreal](https://cesium.com/blog/2021/03/11/reality-capture-models-arriving-in-cesium-for-unreal/)
- [Cesium for Unreal coming March 30](https://cesium.com/blog/2021/02/16/cesium-for-unreal-news/)
- [First Glimpse of Cesium for Unreal to premiere at vIITSEC](https://cesium.com/blog/2020/11/30/project-anywhere/)
- [Project Anywhere](https://www.unrealengine.com/zh-CN/industry/project-anywhere)
- [Cesium for Unreal Now Available](https://cesium.com/blog/2021/03/30/cesium-for-unreal-now-available/)



文中链接较多建议[原文地址](https://github.com/liuvigongzuoshi/blog/blob/master/README.md#%E5%8F%AF%E8%A7%86%E5%8C%96%E7%9B%B8%E5%85%B3)查阅。