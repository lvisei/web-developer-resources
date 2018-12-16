## docker 基础学习

### 1. 验证是否安装成功
```
$ docker version
# 或者
$ docker info
```

### 2. image 文件
```
# 列出本机的所有 image 文件。
$ docker image ls

# 删除 image 文件
$ docker image rm [imageName]
```

### 3. 创建实例
- 将 image 文件从仓库抓取到本地
```
$ docker image pull library/hello-world
# 或者
$ docker image pull hello-world
```
- 运行 image 文件，生成一个正在运行的容器实例
```
$ docker container run hello-world
```
- `docker container run` 命令具有自动抓取 `image` 文件的功能。如果发现本地没有指定的 `image` 文件，就会从仓库自动抓取。因此，前面的docker image pull命令并不是必需的步骤

### 4. 容器文件
image 文件生成的容器实例，本身也是一个文件，称为容器文件。也就是说，一旦容器生成，就会同时存在两个文件： image 文件和容器文件。而且关闭容器并不会删除容器文件，只是容器停止运行而已
```
# 列出本机正在运行的容器
$ docker container ls

# 列出本机所有容器，包括终止运行的容器
$ docker container ls --all
```
终止运行的容器文件，依然会占据硬盘空间，可以使用 `docker container rm` 命令删除
```
$ docker container rm [containerID]
```

### 5. Dockerfile 文件
配置 image，Docker 根据 该文件生成二进制的 image 文件。