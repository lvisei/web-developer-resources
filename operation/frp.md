# 记一次使用`frp`完成内网服务器实现穿透全过程

## 写在前面

因之前内网穿透一直使用的 [ngrok](https://github.com/inconshreveable/ngrok)，使用的 `ngrok`  1.x 版本的最新 `1.7.1`，而`1.7.1`有严重的内存泄露问题，再加上 1.x 早已不在维护，`ngrok 2.x` 后已经不再开源，所以考虑换一个内网穿透工具。

##  一、业务场景

**装备情况**如下：

- 一台阿里云服务器（Windows Server 2012）、公网IP。
- 三台内网服务器，其中两台 Windows Server，一台 CentOS。
- 一个一级域名，已解析到公网IP。

**需求**：通过域名或IP可访问三台内网部署的 `http` 服务、通过域名或IP可通信三台内网的远程桌面或 `SSH`

## 二、为什么选择  [frp](https://github.com/fatedier/frp)

- 开源，维护频繁。
- 支持 TCP、UDP、HTTP、HTTPS、STCP 等协议。
- `GoLang`开发占用内存小，代理稳定。
- 配置简单方便，自定义插件化配置以及可开发自己业务需求的插件。
- 提供安全地暴露内网服务、加密与压缩、底层通信可选 kcp 协议、端口复用、负载均衡、健康检查、URL 路由、范围端口映射、请求的 header处理等众多功能。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfgnqvey96j30es0co3z9.jpg)

初次之前还可通过浏览器查看 frp 的状态以及代理统计信息展示，代理服务器上面，可在浏览器里面访问的各代理状态的可视化仪表盘。

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

进入 [frp](https://github.com/fatedier/frp/releases) 的 GitHub releases 地址，查看最新版，上面已提供各常用操作系统架构编译过的的包，下载对应云服务器系统的版本。

你也可以下载源码，安装`GoLang`环境进行自己编译。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfgnpgfeh2j30m40eomz4.jpg)

### 3. 配置`frps`

下载完成后，解压打开文件夹，找到 frps.ini 文件进行配置服务端代理规则，frpc 开头的文件代理服务器上面不会用，在配置内网的服务器上面会用到。

 将 frps.ini 配置基本内容如下：

```ini
[common]
# 设置地址及通信端口
bind_addr = 0.0.0.0
bind_port = 7000

# 设置默认的UDP端口
bind_udp_port = 7001

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

如果服务器是 Windows系统推荐使用 [winsw](https://github.com/winsw/winsw) 工具将 `frps` 配置成系统服务。

- 下载  *WinSW.exe* or *WinSW.zip*，更改 WinSW.exe 名称为 frps-service.exe

- 编写 frps-service.xml 配置文件

  ```xml
  <service>
   <id>frps</id>
   <name>frps</name>
   <description>frps</description>
   <logmode>roll</logmode>
   <depend></depend>
   <executable>path/frps.exe</executable>
   <arguments>-c frps.ini</arguments>
   <delayedAutoStart/>
   <onfailure action="restart" delay="20 sec"/>
  </service>
  ```

- 安装 `frps` 到系统服务中 `frps-service install`

- 启动服务 `frps-service start`

如果服务器是 Linux 系统，在下载`frp`解压目录的 systemd 目录下面已经有提供将 `frps ` 配置成系统服务的文件。

## 四、客户端配置

### 1. 下载`frp`

进入 [frp](https://github.com/fatedier/frp/releases) 的 GitHub releases 地址，查看已经编译好的最新版，下载对应代理器系统的版本。

### 2. 配置`frpc`

下载完成后，解压打开文件夹，找到 frpc.ini 文件进行配置服务端代理规则，同理 frps 开头的文件在内网服务器上面不会用到。

 将 frpc.ini 配置基本内容如下：

```ini
[common]
# 云服务器的IP地址及 frps 里面设置的通信端口
server_addr = x.x.x.x
server_port = 7000

# 授权 token 与 frps 配置的一样
token = 123456789

# 设置日志文件记录路径
log_file = ./logs/frps.log
# 设置日志记录级别，分别有trace, debug, info, warn, error
log_level = info
# 设置日志记录最大天数
log_max_days = 1

# 设置可在浏览器里面配置热更新的服务
admin_addr = 127.0.0.1
admin_port = 7400
admin_user = admin
admin_pwd = admin

# tcp 范围 7001-7010

# RDP，即Remote Desktop 远程桌面，Windows的RDP默认端口是3389，协议为TCP
[rdp]
type = tcp
local_ip = 127.0.0.1           
local_port = 3389
remote_port = 7001
subdomain = rdp

# SMB，即Windows文件共享所使用的协议，默认端口号445，协议TCP，本条规则可实现远程文件访问。
#[smb]
#type = tcp
#local_ip = 127.0.0.1
#local_port = 445
#remote_port = 7002

# 代理本机 SSH
#[ssh]
#type = tcp
#local_ip = 127.0.0.1
#local_port = 22
#remote_port = 7003

# 设置本地端口80 HTTP 服务的代理
# 可通过 frps 里面配置的 subdomain_host 域名
# 访问 test.example.com 加 HTTP 代理端口，即 http://test.example.com:7080
[web_test]
type = http
local_port = 80
subdomain = test
```

### 4. 启动 `frpc`

启动`frpc`的服务与`frps` 类似。

```bash
./frpc -c ./frpc.ini
```

结下来就可以访问测试你穿透的远程连接、SSH、web服务等。

### 5. 将 `frpc` 配置成系统服务

这里不再赘述，与配置将 `frps` 配置成系统服务类似。

## 四、HTTPS 配置

将内网穿透的 HTTP 服务配置成 HTTPS 主要有两种方法：

1. 通过 `frpc` 的 `https2http` 插件将本地 HTTP 服务启用 HTTPS，通过这种方式比较方便简单对需要 HTTPS 的服务进行可控性的配置，但如果各个内外服务器需要 HTTPS 穿透的服务很多的话，就需要给每一个穿透服务生成并配置一个证书，这样就比较繁琐了。

2. 通过生成泛域名证书，在外网服务器上面进行配置 Nginx ，将内网穿透出来的 Web 服务进行代理转发。

这里主要介绍第二种方式进行 HTTPS 配置

### 1. 生成泛域名证书

### 2. 配置 Nginx

### 3. 证书更新


### 参考资料

- [frp 使用文档](https://github.com/fatedier/frp/blob/master/README.md)



*原文首发地址 [https://github.com/liuvigongzuoshi/summarize-web-resources/blob/master/operation/frp.md](https://github.com/liuvigongzuoshi/summarize-web-resources/blob/master/operation/frp.md)*