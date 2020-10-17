# 使用 acme.sh 自动从 letsencrypt 生成证书安装到Nginx

## 一、安装 acme.sh

```bash
curl  https://get.acme.sh | sh
```

重新载入 `.bashrc`

```shell
source ~/.bashrc 
```

## 	二、生成证书

**acme.sh** 支持两种 HTTP 和 DNS 验证方式验证域名所有权，DNS 验证方式有自动与手动方式，自动方式验证是使用域名解析商提供的 API 自动添加 txt 记录完成验证，**acme.sh** 支持上百种解析商的自动集成验证域名所有权。

以下使用**acme.sh** 支持的阿里云 ，自动验证域名所有权。

第一步登录你的阿里云账号获取你的API key，获取阿里云的 AccessKey [地址](https://usercenter.console.aliyun.com/#/manage/ak)，建议创建使用子帐户生成AccessKey，给子账户分配管理云解析（DNS）的权限。

拿到Key和Secre后，到终端生成环境变量。

```shell
export Ali_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export Ali_Secret="jlsdflanljkljlfdsaklkjflsa"
```

第二步生成证书，`-d ` 后面是一个域名，也可以一次生成多个域名证书。

```shell
acme.sh --issue --dns dns_ali -d example.com -d www.example.com
```

有需求可直接生成一个通配符域名证书。

```shell
acme.sh  --issue -d example.com  -d '*.example.com'  --dns dns_ali
```

证书生成完成之后**acme.sh** 会自动记保存API ID 和 API key，会保存到 `~/.acme.sh/account.conf`，下次再使用阿里云API的时候不需要再指定阿里云的 AccessKey了。

## 三、安装证书

前面生成的证书默认都会生成到`~/.acme.sh/`目录下面。

以 Nginx 为例安装证书

```shell
acme.sh --installcert -d '*.example.com' \
--key-file       /etc/nginx/cert/'*.example.com.key'  \
--fullchain-file /etc/nginx/cert/'*.example.com.pem' \
--reloadcmd     "sudo service nginx force-reload"
```

以上命令会将证书拷贝复制到 `/etc/nginx/cert/`目录下，并重启 Nginx 。

上面指定的所有参数都会被自动记录下来，并在将来证书自动更新以后， 会被再次自动调用。

需要注意到是，上面的`--installcert`命令后面跟的 `sudo service nginx force-reload` 参数，要成功执行的前提需要将Nginx 添加到系统的服务中并设置相关script，更多关于添加到服务中的设置查看 [Red Hat NGINX Init Script](https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/)。

安装证书并配置完成后访问 [SSL Server Test](https://www.ssllabs.com/ssltest/index.html)，输入域名测试证书。

##  四、更新证书

更新证书不需要做任何操作， **acme.sh** 会自动创建 cronjob，每天 0:00 点自动检测所有的证书，如果证书快过期了，则会自动更新证书。



### 参考资料

- [An ACME Shell script: acme.sh](https://github.com/acmesh-official/acme.sh)
-  [ acme.sh 使用说明](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)
-  [使用 acme.sh 给 Nginx 安装 Let’ s Encrypt 提供的免费 SSL 证书](https://ruby-china.org/topics/31983)



*原文首发地址 [https://github.com/liuvigongzuoshi/blog](https://github.com/liuvigongzuoshi/blog#%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BF%90%E7%BB%B4%E7%9B%B8%E5%85%B3)*

