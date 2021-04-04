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

如果你在使用 `Unreal` 开发游戏，现在需要用到真实世界某个大雪山（地形数据）作为游戏场景，那么现在可以使用 [Cesium for Unreal](https://www.unrealengine.com/marketplace/zh-CN/product/87b0d05800a545d49bf858ef3458c4f7) 这个插件， 就可以将 3D 真实世界的地理空间数据加载到 `Unreal` 中使用了，也就是说游戏中虚拟世界它可以来源于我们物理设备采集的真实世界的空间数据，以后你在游戏里感觉到的真实世界与虚拟世界之间的界限也就越了越模糊了。

如果多年以后我们的 VR 游戏和智能穿戴设备发展到较高水平时，将我们真实世界带到数据的虚拟世界中，在家足不出户就可以玩游戏一样旅游看名山大川，在里面社交和去想去的地方重新生活，是不是就跟斯皮尔伯格的《头号玩家》一样，人们对混乱和崩溃边缘的现实世界的失望，最后大家会不会望寄托于《绿洲》的虚拟世界呢？

## 在 Unreal Engine 里面使用 Cesium for Unreal 插件

下面是在 Unreal Engine 里面使用 Cesium 的地理空间数据的效果图

|                                                              |                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![CesiumforUnrealAlps-1920x1080-f715e99b688236579e7a6c150b83a29d](https://tva1.sinaimg.cn/large/008eGmZEgy1gp872ppz75j31hc0u015m.jpg) | ![CesiumforUnrealDenverCharacter-1920x1080-cec3f71f713066603ebab1762f2a8720](https://tva1.sinaimg.cn/large/008eGmZEgy1gp872zls6kj31hc0u0tmr.jpg) | ![CesiumforUnrealDenverUnionStation-1920x1080-e0caf247b778a4f404bd48fd8f9a2f4e](https://tva1.sinaimg.cn/large/008eGmZEgy1gp8739xxqkj31hc0u07l5.jpg) |
| ![CesiumforUnrealDrone-1920x1080-a799691c0b254da7f6de3bf59a633f10](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873e3iihj31hc0u018x.jpg) | ![CesiumforUnrealDrone2-1920x1080-9f42c3fc1af6e7ae1973f5d622e77a7e](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873gxfqsj31hc0u0tiw.jpg) | ![CesiumforUnrealMelbourne-1920x1080-ead9a464ce01934453c75b7a343682ec](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873m9godj31hc0u01de.jpg) |
| ![CesiumforUnrealMelbournePlane-1920x1080-eb154db558f373847e1c4fa7c38cbe2d](https://tva1.sinaimg.cn/large/008eGmZEgy1gp873xajldj31hc0u0qkk.jpg) | ![CesiumforUnrealCharacter-1920x1080-71feb9a07b10330fba6ff540c4a5f9e5](https://tva1.sinaimg.cn/large/008eGmZEgy1gp874g13bzj31hc0u0tru.jpg) | ![RealityCaptureInt01-1920x1080-a1591035365feb374be20f4b32abc465](https://tva1.sinaimg.cn/large/008eGmZEgy1gp8744s4k5j31hc0u0ap1.jpg) |

官方的两个宣传视频



在 Unreal Engine 里面操作截图

|                                                              |                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![7BBC42C2F0E4EC15761D76D319AF984B](https://tva1.sinaimg.cn/large/008eGmZEgy1gp87ux5fp7j31f20u0dlq.jpg) | ![20210401-0](https://tva1.sinaimg.cn/large/008eGmZEgy1gp87v6xw0tj319f0lz41l.jpg) | ![20210401-1](https://tva1.sinaimg.cn/large/008eGmZEgy1gp87vazj18j31hc0metcn.jpg) |
| ![img](https://images.prismic.io/cesium/78453878-e7e8-4a44-9f64-1503575c0ab3_unreal-announcement-1.jpg?auto=compress%2Cformat&w=944) | ![img](https://images.prismic.io/cesium/27307e53-46f2-4e54-bbe1-6e02bc75626e_unreal-announcement-2.png?auto=compress%2Cformat&w=944) |                                                              |



官方给的两篇教程，很简单按到步骤来弄就算了，不过对电脑的配置要求很高，不然你的电脑恐怕是流程走不完。

- [Cesium for Unreal Quickstart](https://cesium.com/docs/tutorials/cesium-unreal-quickstart/)
- [Cesium for Unreal Getting Started Tutorials](https://cesium.com/docs/tutorials/cesium-unreal-020-photogrammetry/)

要提一下的是虽然虚幻引擎是跨平台的，支持 linux 和 Mac OSX 版本下载安装，但目前 Cesium for Unreal 插件只支持 Windows 平台。

![Cesium for Unreal plugin](https://tva1.sinaimg.cn/large/008eGmZEgy1gp85v8tti1j31ls0u01kx.jpg)

## Cesium for Unreal 的技术点

### 关于插件技的术路线

都知道目前 `Cesium` 是基于 `WebGl` 建设的，也就是 `JS` 写的 3D 渲染引擎，能在支持 `WebGl` 的浏览器环境中运行，而 `Unreal` 是基于 `C++` 开发的桌面游戏开发引擎，那如何让 `Cesium` 生态里面的数据源能在 `Unreal` 里面使用的呢？

下图 Cesium for Unreal 的架构图，



![unreal-architecture](https://tva1.sinaimg.cn/large/008eGmZEgy1gp87xfmj21j31g50u010o.jpg)



### 支持的数据格式



支持哪些数据源



是否把坐标和投影带入





## 写在后面

最后分享一段 Cesium 作者 Patrick 在 [Cesium for Unreal Now Available](https://cesium.com/blog/2021/03/30/cesium-for-unreal-now-available/) 中提到关于未来的期望。

> We live in an exciting time of ubiquitous sensors capturing the real world; drones and autonomous vehicles carrying sensors; photogrammetry and AI algorithms creating semantic 3D models; data explosion in resolution and collection frequency; plentiful cloud compute and storage; AR and VR devices coming of age; 5G bandwidth on the rise; GPUs that rival past supercomputers; and cell phones that rival yesteryear’s laptops. My prediction is that the dividing line between the physical and digital worlds will continue to blur, as will the line between 3D geospatial and game assets and ecosystems. This ecosystem of ecosystems will be enabled by open standards and platforms of platforms. 3D Tiles may very well become the spatial temporal index for all of it. I won’t be a graphics programmer in geospatial; I will just be a programmer.
>
> One prediction is certain: you will create the future; our role is just to facilitate it.
>
>   --- You and The Future

## 链接

- [First Look at RealityCapture Photogrammetry in Cesium for Unreal](https://cesium.com/blog/2021/03/11/reality-capture-models-arriving-in-cesium-for-unreal/)
- [Cesium for Unreal coming March 30](https://cesium.com/blog/2021/02/16/cesium-for-unreal-news/)
- [First Glimpse of Cesium for Unreal to premiere at vIITSEC](https://cesium.com/blog/2020/11/30/project-anywhere/)
- [Project Anywhere](https://www.unrealengine.com/zh-CN/industry/project-anywhere)
- [Cesium for Unreal Now Available](https://cesium.com/blog/2021/03/30/cesium-for-unreal-now-available/)

