# golang-resources

## Article

### Concurrency

- [Concurrency is not Parallelism](https://talks.golang.org/2012/waza.slide#1) - Rob Pike 的并发不是并行演讲， 附[译文](https://www.cyningsun.com/12-09-2019/concurrency-is-not-parallelism.html)及[视频](https://www.youtube.com/watch?v=cN_DpYBzKso)。
- [20 张动图为你演示 Go 并发](https://learnku.com/go/t/39490) - 并发可视化讲解。

### Project Layout

- [Standard Go Project Layout](https://github.com/golang-standards/project-layout) - 标准 Go 项目布局。

### Compilation

- [初识 Golang 汇编](https://mp.weixin.qq.com/s/MFN9eqT9bsotvpV-m1eqCQ) - 腾讯VTeam技术团队分享深入理解 Go 汇编。
- [Go 编译器定制简介](https://wa-lang.org/ugo-compiler-book/talks/go-compiler-intro.html#1) - 基于 Go 从零实现 [µGo](https://github.com/wa-lang/ugo) 语言的[编译器](https://github.com/wa-lang/ugo-compiler-book)，最终以 Clang 执行编译为 LLVM 的汇编语言。

### GC garbage collection

- [垃圾回收与Go的选择](https://mp.weixin.qq.com/s/bT97HqLHyrE1RkKoGAC11w) - 垃圾回收的5种经典策略及 Go 选择。

## Open source library

### CLI

- [Create Go App CLI](https://github.com/create-go-app/cli) - 帮助创建一个应用框架，包含后端（Go）、前端（JavaScript、TypeScript）和自动化部署，更专注业务逻辑开发。
- [infinite](https://github.com/fzdwx/infinite) - 用于开发交互式 CLI 程序的组件库，提供一系列开箱即用的组件，目前支持 window/linux。

### Concurrency

- [ants](https://github.com/panjf2000/ants) - 一个高性能的 goroutine 池，实现了对大规模 goroutine 的调度管理、goroutine 复用，在开发并发程序的时候限制 goroutine 数量，复用资源，达到更高效执行任务的效果。

### Web Framework

- [Gin Web Framework](https://github.com/gin-gonic/gin) - 一个用 Go 编写的 HTTP web 框架。 

### ORM

- [GORM](https://github.com/go-gorm/gorm) - 一个友好的对象关系映射模型库。

### Authentication and OAuth

- [jwt-go](https://github.com/dgrijalva/jwt-go) - Golang 实现的 JSON Web Tokens (JWT) 库。
- [Casbin](https://github.com/casbin/casbin) - 一个强大的、高效的开源访问控制框架，其权限管理机制支持多种访问控制模型。
- [Go Captcha](https://github.com/wenlng/go-captcha) - 图像交互式安全验证码，可用于机器人验证。

### Messages in real-time

- [gotify/server](https://github.com/gotify/server) - 一个 Go 语言写的 WebSockets 库，有服务端、客户端和安卓端。


### Miscellaneous

#### Dependency Injection

- [Wire](https://github.com/google/wire) -   Google 开源的一个依赖注入工具。

#### GC

- [goleak](https://github.com/uber-go/goleak) - 结合单元测试快速的检测 goroutine 泄露，同类型库 [gotrace](https://github.com/ysmood/gotrace)。


### RPC

-

### Micro Services

- [Go kit](https://github.com/go-kit/kit) - A standard library for microservices.
- [Go Micro](https://github.com/micro/go-micro) - A Go standard library for microservices.
- [Gizmo Microservice Toolkit](https://github.com/nytimes/gizmo) - A Microservice Toolkit from The New York Times.


### Coding Standards 

- [Uber Go 语言编码规范](https://github.com/xxjwxc/uber_go_guide_cn) - 2018 年年末 Uber 将内部的 Go 风格规范 开源到 GitHub，经过一年的积累和更新，该规范已经初具规模，并受到广大 Gopher 的关注。

