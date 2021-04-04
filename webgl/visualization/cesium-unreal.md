# 初探 Cesium for Unreal

## 写在前面

### cesium?

这个没有啥说的，象征性的贴张图片和链接。

- 官网 - [cesium.com](https://cesium.com/)
- 在线示例功能 - [sandcastle.cesium.com](https://sandcastle.cesium.com/)

![image-20210404233339360](https://tva1.sinaimg.cn/large/008eGmZEgy1gp84v33q0xj31ls0u01av.jpg)



### unreal engine?

什么是虚幻引擎呢？

> 虚幻引擎是全球最开放、最先进的实时3D创作平台。经过持续的改进，它已经不仅仅是一款殿堂级的游戏引擎，还能为各行各业的专业人士带去无限的创作自由和空前的掌控力。无论是前沿内容、互动体验还是沉浸式虚拟世界，一切尽在虚幻引擎。  
>
> ​	--- 来自[官网](https://www.unrealengine.com/zh-CN/)

如果没有虚幻引擎是什么也没有关系，《绝地求生》和《堡垒之夜》总有听说过吧，就是虚幻引擎这玩意做的。Unreal 是 Epic Games 公司开发的游戏引擎，关于 Epic Games ∂的了解可以看看阮老师的这篇文章---《[Epic Games 研究：Fortnite 和游戏行业的未来](https://www.ruanyifeng.com/blog/2020/06/epic-games.html)》。

### Cesium for Unreal ？

Cesium for Unreal 有什么意义呢？如果你使用 `Unreal` 开发游戏，需要用到真实世界一些的地形和模型等什么的，那么现在可以将 3D 真实世界的地理空间数据带到 `Unreal` 中了

## 在 Unreal engine 里面使用 Cesium 插件的效果





## 在 Unreal 里面使用 Cesium 

### 技术流程

都知道目前 `Cesium` 是基于 `WebGl` 建设的，也就是 `JS` 写的 3D 渲染引擎，能在支持 `WebGl` 的浏览器环境中运行，而 `Unreal` 是基于 `C++` 开发的桌面游戏开发引擎，那如何让 `Cesium` 生态里面的数据源能在 `Unreal` 里面使用的呢？




- 支持哪些数据源
- 是否把坐标和投影带入



## 链接

- [First Look at RealityCapture Photogrammetry in Cesium for Unreal](https://cesium.com/blog/2021/03/11/reality-capture-models-arriving-in-cesium-for-unreal/)
- [Cesium for Unreal coming March 30](https://cesium.com/blog/2021/02/16/cesium-for-unreal-news/)
- [First Glimpse of Cesium for Unreal to premiere at vIITSEC](https://cesium.com/blog/2020/11/30/project-anywhere/)