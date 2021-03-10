## interview questions

### 1、JavaScript 的 var，let 和 const 区别

| 类型			|块作用域 	| 暂存死区 			| 创建全局属性 | 可重新分配 | 可重新声明|
| :------- | :------- | :----------- | :--------- | :--------- | ---- |
| `var`    | ❌        | ❌            | ✅          | ✅          | ✅    |
| `let`    | ✅        | ✅            | ❌          | ✅          | ❌    |
| `const`  | ✅        | ✅            | ❌          | ❌          | ❌    |

### 2、 输入url后发生了什么

从耗时过程来看，可以分为DNS解析、TCP连接、HTTP请求与响应、客户端浏览器解析渲染、连接结束。

其中浏览器解析渲染包含HTML词法、语法的解析、CSS解析、构造CSSOM树、DOM树生成、渲染树建立、屏幕绘制。

### 2.1 http2 的多路复用

http/1.0：如需要发送多个请求必须创建多个 TCP 连接，并且浏览器对于单域名请求有数量限制（一般6个），其连接无法被复用，相对于 HTTP1.x 的文本格式，二进制格式解析更高效。

http/1.1：引入流水线（Pipelining）技术，但先天 FIFO（先进先出）机制导致当前请求的执行依赖于上一个请求执行的完成，容易引起报头阻塞，并没有从根本上解决问题。

http/2：重新定义底层 http 语义映射，允许同一个连接上使用请求和响应双向数据流。同一域名只需占用一个 TCP 连接，通过数据流（Stream）以帧为基本协议单位，从根本上解决了问题，避免了因频繁创建连接产生的延迟，减少了内存消耗，提升了使用性能。

### 3、性能优化从哪些方面入手

- 分屏加载，当页面需要渲染的数据较多时，先渲染首屏，下滑时再加载第二屏的数据；
- 图片大小优化，在不影响视觉效果的前提下，把图片尺寸降到最小；
- 图片懒加载，on appear时再加载图片；
- Code splitting，或者拆包，应用下的某些组件不需要立刻import，可以采用动态import的方式，打包时也可以将它们打到不同的bundle里，给index bundle瘦身；
- Chrome Devtools - Trace & Timeline等一系列强大的分析工具可以去研究一下，它们可以深入到内核分析应用的性能问题所在；

- [前端性能优化 24 条建议（2020）](https://juejin.cn/post/6892994632968306702)

### 4、如何加快首屏渲染，有哪些方案?

- 降低请求量：合并资源，减少 HTTP 请求数，minify / gzip 压缩，webP，lazyload。

- 加快请求速度：预解析DNS，减少域名数，并行加载，CDN 分发。

- 增加缓存：HTTP 协议缓存请求，离线缓存 manifest，离线数据缓存 localStorage、PWA。

- 渲染优化：首屏内容最小化，JS/CSS优化，加载顺序，服务端渲染，pipeline。

### 5、浏览器怎么进行垃圾回收，变量何时释放，哪些情况会导致内存泄露，`weakMap` `weakSet` 和 `Map` `Set` 有什么区别？

- [从 4 个面试题了解「浏览器的垃圾回收」](https://juejin.cn/post/6861967094318284814#heading-0)
- [前端内存泄漏及万善的解决方案](https://juejin.cn/post/6914092198170460168)

### 6. 为什么 Vue 中不要用 index 作为 key？

- [面试官：为什么 Vue 中不要用 index 作为 key？（diff 算法详解）](https://mp.weixin.qq.com/s/DRIYDutR2BcKzMs5CkycQg)

### 7. 兼容多种模块规范

```js
;(function (name, definition) {
  // 检测上下文环境是否为AMD或CMD
  var hasDefine = typeof define === 'function',
    // 检查上下文环境是否为Node
    hasExports = typeof module !== 'undefined' && module.exports;

  if (hasDefine) {
    // AMD环境或CMD环境
    define(definition);
  } else if (hasExports) {
    // 定义为普通Node模块
    module.exports = definition();
  } else {
    // 将模块的执行结果挂在window变量中，在浏览器中this指向window对象
    this[name] = definition();
  }
})('hello', function () {
  var hello = function () {};
  return hello;
});
```

