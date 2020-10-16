# CocoaPods 使用疑惑点

## 一、要不要提交你的 `Podfile.lock` 文件到仓库？

团队开发强烈建议提交你的 `Podfile.lock` 到代码版本管理仓库里，即使你不将 `Pods`文件夹提交到仓库中，你也应该始终提交并推送`Podfile.lock`文件到你的仓库中。 

如果你不这样做，这样会打破`pod install`能够锁定的`pod`安装版本的整个逻辑，也就是说团队里面另一个成员拉去了你的代码，然后执行`pod install`，它会安装`Podfile`文件里面指定依赖的版本的可更新最新版本的依赖。

举例来说，你`Podfile`文件里面写了一个 A 依赖，版本制定`~> 1.0'`，然后你安装使用了并未提交`Podfile.lock`文件到仓库中，这个时候你的团队成员拉下代码，准备`pod install`安装依赖，假如此时 A 依赖已经发布`1.3`修复某个 bug 的版本并新增了一些 API，但可能 bug 修复了但又新增了一个致命性的 bug，此时你的团队成员`pod install`之后，会安装`1.0.1`这个版本，最终导致你俩安装使用依赖的版本不一致，最后可能导致他拉下来的代码就是跑不起来。

过了个人不建议提交`Pods`文件夹提交到仓库中，因为项目依赖项比较多的话，会导致提交很多碎小的文件，代码版本管理的时候极为不友好。你应该总是提交`Podfile` 与`Podfile.lock` 文件到仓库中，这样团队成员可以自己下载与你安装版本一致到依赖。

## 二、为什么有时候只使用 `Podfile` 锁定不了版本？

## 三、`pod install`、 `pod outdated`、`pod update` 区别是什么，分别应该什么时候使用？



### 参考资源

- [pod install vs. pod update](https://guides.cocoapods.org/using/pod-install-vs-update.html)

