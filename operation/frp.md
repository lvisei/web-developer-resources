# 记一次使用`frp`完成我司内部服务器实现内网穿透全过程

## 写在前面

因之前内网穿透一直使用的 [ngrok](https://github.com/inconshreveable/ngrok)，使用的 `ngrok`  1.x 版本的最新 `1.7.1`，而`1.7.1`有严重的内存泄露问题，再加上 1.x 早已不在维护，`ngrok 2.x` 后已经不再开源，所以考虑换一个内网穿透工具。

##  一、业务场景

**装备情况**如下：

- 一台阿里云服务器（Windows Server 2012）、公网IP。
- 三台内网服务器，其中两台 Windows Server，一台 CentOS。
- 一个一级域名，已解析到公网IP。

**需求**：通过域名或IP可访问三台内网部署的 `http` 服务、通过域名或IP可通信三台内网的远程桌面或 `SSH`

## 二、为什么选择 `frp`

开源，维护频繁，Go语言开发占用内存小，代理效率高、配置简单，自定义插件化配置以及可开发自己业务需求的插件。

![img](https://raw.githubusercontent.com/fatedier/frp/master/doc/pic/architecture.png)

提供可访问的Dashboard面板服务

![img](https://github.com/fatedier/frp/blob/master/doc/pic/dashboard.png?raw=true)

## 三、服务端配置

1.

10. 配置到系统服务，使用[winsw](https://github.com/winsw/winsw)
11. 



## 四、客户端配置