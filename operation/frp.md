# 记一次使用`frp`完成内网服务器实现穿透全过程

## 写在前面

因之前内网穿透一直使用的 [ngrok](https://github.com/inconshreveable/ngrok)，使用的 `ngrok`  1.x 版本的最新 `1.7.1`，而`1.7.1`有严重的内存泄露问题，再加上 1.x 早已不在维护，`ngrok 2.x` 后已经不再开源，所以考虑换一个内网穿透工具。

##  一、业务场景

**装备情况**如下：

- 一台阿里云服务器（Windows Server 2012）、公网IP。
- 三台内网服务器，其中两台 Windows Server，一台 CentOS。
- 一个一级域名，已解析到公网IP。

**需求**：通过域名或IP可访问三台内网部署的 `http` 服务、通过域名或IP可通信三台内网的远程桌面或 `SSH`

## 二、为什么选择 `frp`

开源，维护频繁，Go语言开发占用内存小，代理稳定、配置简单，自定义插件化配置以及可开发自己业务需求的插件。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfgnqvey96j30es0co3z9.jpg)

代理服务器上面，可在浏览器里面访问的各代理状态的可视化仪表盘。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfgnr8wpxrj31pw0u0tc0.jpg)

内网穿透服务器上提供可查看各穿透状态、动态在线配置、热更新配置功能。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfgp4ofvdfj31yh0twgrh.jpg)

## 三、服务端配置

### 1. 开放云服务器的端口
登录阿里云服务平台，进入实例安全组，开放穿透代理需要的端口，先开放端口如下：

- 开放TCP端口，7000至7010端口，7000端口是 `frp`服务端代理通信的默认端口，7001至7010端口可代理内网服务器的TCP应用程序的端口，如果不够使用可将端口继续延长。
- 开放TCP端口，7080与7443端口，方便进行 HTTP 与HTTPS代理，因云服务器80与443端口已经被其他应用程序占用，取名后缀80与443方便记忆维护。
- 开放UDP端口，开放7001端口，如果有需要提供点对点内网穿透可开启。

以上端口开启不是指定，可自定义灵活配置。

### 2. 下载`frp`

进入 [frp](https://github.com/fatedier/frp/releases) 的 GitHub releases 地址，查看已经编译好的最新版，下载对应云服务器系统的版本。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfgnpgfeh2j30m40eomz4.jpg)

### 3. 配置`frps`

下载完成后，解压打开文件夹，找到 frp.ini 文件进行配置服务端代理规则，frpc 开头的文件代理服务器上面不会用，在配置内网的服务器上面会用到。

 将 frp.ini 配置基本内容如下：

```ini
[common]
# 设置地址及通信端口
bind_addr = 0.0.0.0
bind_port = 7000

# 设置默认的UDP端口
bind_udp_port = 7001

# if you want to support virtual host, you must set the http port for listening (optional)
# 监听7080与7443端口，进行 HTTP 与 HTTPS 代理，HTTP 与 HTTPS 端口可以成设置一样
vhost_http_port = 7080
vhost_https_port = 7443

# 设置查看仪表板服务地址及端口，dashboard_addr 不设置，默认与 bind_addr 一样，如果不设置 dashboard_port 端口不会开启这个服务
dashboard_addr = 0.0.0.0
dashboard_port = 7500

# 设置仪表盘服务的登录的账号与密码，如果不设置就默认都是 admin
dashboard_user = admin
dashboard_pwd = admin

# 设置云服务器的域名，方便简单的配置代理出去的服务可以通过子域名的方式访问
subdomain_host = example.com

# 设置Token, 尽量配置复杂些，配置 frpc 会用到
token = 123456789

# 设置日志文件记录路径
log_file = ./logs/frps.log
# 设置日志记录级别，分别有trace, debug, info, warn, error
log_level = info
# 设置日志记录最大天数
log_max_days = 1
```



### 4. 启动 `frps`

启动`frps`的服务很简单，输入如下命令，代理服务端就已经启动起来了。

```bash
./frps -c ./frps.ini
```

### 5. 将 `frps` 配置成系统服务

如果服务器是 Windows系统推荐使用 [winsw](https://github.com/winsw/winsw) 工具将 `frps` 配置成系统服务

如果服务器是 



## 四、客户端配置