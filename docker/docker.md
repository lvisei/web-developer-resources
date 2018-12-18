## docker 基础学习

### 1. 验证是否安装成功

```bash
docker version
# 或者
docker info
```

### 2. image 文件

```bash
# 列出本机的所有 image 文件。
docker image ls

# 删除 image 文件
docker image rm [imageName]
```

### 3. 创建实例

- 将 image 文件从仓库抓取到本地

```bash
docker image pull library/hello-world
# 或者
docker image pull hello-world
```

- 运行 image 文件，生成一个正在运行的容器实例

```bash
docker container run hello-world
```

- `docker container run` 命令具有自动抓取 `image` 文件的功能。如果发现本地没有指定的 `image` 文件，就会从仓库自动抓取。因此，前面的 docker image pull 命令并不是必需的步骤

### 4. 容器文件

image 文件生成的容器实例，本身也是一个文件，称为容器文件。也就是说，一旦容器生成，就会同时存在两个文件： image 文件和容器文件。而且关闭容器并不会删除容器文件，只是容器停止运行而已

```bash
# 列出本机正在运行的容器
docker container ls

# 列出本机所有容器，包括终止运行的容器
docker container ls --all
```

终止运行的容器文件，依然会占据硬盘空间，可以使用 `docker container rm` 命令删除

```bash
docker container rm [containerID]
```

### 5. 制作 Docker 容器

#### 5.1 编写 Dockerfile 文件

在项目的根目录下，新建一个文本文件 `.dockerignore` ，写入下面的内容。

```
.git
node_modules
```

上面代码表示，这些路径要排除，不要打包进入 image 文件。

然后，在项目的根目录下，新建一个文本文件 Dockerfile，写入下面的内容。

```bash
FROM node:8.4
# 该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
COPY . /app
# 将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
WORKDIR /app
# 指定接下来的工作路径为/app。
RUN npm install --registry=https://registry.npm.taobao.org
# 在/app目录下，运行npm install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
EXPOSE 3000
# 将容器 3000 端口暴露出来， 允许外部连接这个端口。
```

#### 5.2 创建 image 文件

有了 Dockerfile 文件以后，就可以使用 `docker image build` 命令创建 image 文件了。

```bash
docker image build -t koa-demo .
# 或者
docker image build -t koa-demo:0.0.1 .
```

上面代码中， `-t` 参数用来指定 image 文件的名字，后面还可以用冒号指定标签。如果不指定，默认的标签就是 `latest` 。最后的那个点表示 Dockerfile 文件所在的路径，上例是当前路径，所以是一个点。

如果运行成功，就可以看到新生成的 image 文件 `koa-demo` 了。

```
docker image ls
```

#### 5.3 生成容器

`docker container run` 命令会从 image 文件生成容器。

```bash
docker container run -p 8000:3000 -it koa-demo /bin/bash
# 或者
docker container run -p 8000:3000 -it koa-demo:0.0.1 /bin/bash
```

上面命令的各个参数含义如下：

- `-p` 参数：容器的 3000 端口映射到本机的 8000 端口。
- `-it` 参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
- `koa-demo:0.0.1`：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
- `/bin/bash`：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。

在容器的命令行，按下 Ctrl + c 停止 Node 进程，然后按下 Ctrl + d （或者输入 exit）退出容器。此外，也可以用 `docker container kill` 终止容器运行。

```bash
# 在本机的另一个终端窗口，查出容器的 ID
docker container ls

# 停止指定的容器运行
docker container kill [containerID]
```

容器停止运行之后，并不会消失，用下面的命令删除容器文件。

```bash
# 查出容器的 ID
docker container ls --all

# 删除指定的容器文件
docker container rm [containerID]
```

也可以使用 `docker container run` 命令的--rm 参数，在容器终止运行后自动删除容器文件。

```
docker container run --rm -p 8000:3000 -it koa-demo /bin/bash
```

#### 5.4 CMD 命令

上面，容器启动以后，需要手动输入命令 node demos/01.js。我们可以把这个命令写在 Dockerfile 里面，这样容器启动以后，这个命令就已经执行了，不用再手动输入了。

```
FROM node:8.4
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
CMD node demos/01.js
```

上面的 Dockerfile 里面，多了最后一行 `CMD node demos/01.js` ，它表示容器启动后自动执行 `node demos/01.js`。

`RUN` 命令与 `CMD` 命令的区别在哪里？简单说，`RUN` 命令在 image 文件的构建阶段执行，执行结果都会打包进入 image 文件；CMD 命令则是在容器启动后执行。另外，一个 Dockerfile 可以包含多个 `RUN` 命令，但是只能有一个 `CMD` 命令。

注意，指定了 `CMD` 命令以后，`docker container run` 命令就不能附加命令了（比如前面的`/bin/bash`），否则它会覆盖 `CMD` 命令。现在，启动容器可以使用下面的命令。

```
docker container run --rm -p 8000:3000 -it koa-demo:0.0.1
```

#### 5.5 发布 image 文件

容器运行成功后，就确认了 image 文件的有效性。这时，我们就可以考虑把 image 文件分享到网上，让其他人使用。

首先，去 hub.docker.com 或 cloud.docker.com 注册一个账户。然后，用下面的命令登录。

```
docker login
```

接着，为本地的 image 标注用户名和版本。

```bash
docker image tag [imageName] [username]/[repository]:[tag]
# 实例
docker image tag koa-demos:0.0.1 ruanyf/koa-demos:0.0.1
```

也可以不标注用户名，重新构建一下 image 文件。

```
docker image build -t [username]/[repository]:[tag] .
```

最后，发布 image 文件。

```
docker image push [username]/[repository]:[tag]
```

发布成功以后，登录 hub.docker.com，就可以看到已经发布的 image 文件。

### 6. 其他有用的命令

docker 的主要用法就是上面这些，此外还有几个命令，也非常有用。

- docker container start

前面的 `docker container run` 命令是新建容器，每运行一次，就会新建一个容器。同样的命令运行两次，就会生成两个一模一样的容器文件。如果希望重复使用容器，就要使用 `docker container start` 命令，它用来启动已经生成、已经停止运行的容器文件。

```
docker container start [containerID]
```

- docker container stop

前面的 `docker container kill` 命令终止容器运行，相当于向容器里面的主进程发出 SIGKILL 信号。而 `docker container stop` 命令也是用来终止容器运行，相当于向容器里面的主进程发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号。

```
container stop [containerID]
```

这两个信号的差别是，应用程序收到 SIGTERM 信号以后，可以自行进行收尾清理工作，但也可以不理会这个信号。如果收到 SIGKILL 信号，就会强行立即终止，那些正在进行中的操作会全部丢失。

- docker container logs

`docker container logs` 命令用来查看 docker 容器的输出，即容器里面 Shell 的标准输出。如果 `docker run` 命令运行容器的时候，没有使用 `-it` 参数，就要用这个命令查看输出。

```
docker container logs [containerID]
```

- docker container exec

`docker container exec` 命令用于进入一个正在运行的 docker 容器。如果 `docker run` 命令运行容器的时候，没有使用 `-it` 参数，就要用这个命令进入容器。一旦进入了容器，就可以在容器的 Shell 执行命令了。

```
docker container exec -it [containerID] /bin/bash
```

- docker container cp

`docker container cp` 命令用于从正在运行的 Docker 容器里面，将文件拷贝到本机。下面是拷贝到当前目录的写法。

```
docker container cp [containID]:[/path/to/file] .
```
