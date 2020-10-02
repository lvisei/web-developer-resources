# iOS 开发环境搭建注意点

## Xcode 安装

在[苹果开发者网站](https://developer.apple.com/xcode/)下载Xcode安装文件或去苹果应用市场安装

安装了 Xcode 就不需要手动再安装 CommandLineTools 

Xcode 内置了CommandLineTools，目录为 `/Applications/Xcode.app/Contents/Developer`，xcode-select 随着 Xcode 自动升级

 单独安装 CommandLineTools，以后可 `xcode-select` 版本切换使用，不是必要步骤

```bash
xcode-select --install
```

安装目录到了 `/Library/Developer/CommandLineTools`

### xcode-select 用法

- `-p, --print-path`： 打印目前指向的路径
- `-s <path>, --switch <path>`: 将路径指向某个 Command Line Tools，`xcode-select --switch /Applications/Xcode.app`
- `-r, --reset`: 将之前用-s 指定的路径重置

## CocoaPods 安装

macOS 都安装了ruby，直接使用ruby 的包管理器 gem 命令即可安装 CocoaPods

### 更新 gem

查看ruby版本 `ruby --version`

查看 gem 的版本号`gem --version`

查看 gem 环境变量 `gem env`， 查看 GEM PATHS  包全局安装路径

更新 gem `sudo gem update --system`

### 安装 [cocoapods](https://guides.cocoapods.org/using/getting-started.html#getting-started)

安装CocoaPods `sudo gem install cocoapods`

### 安装失败

如果安装失败，以下是解决方案

安装失败的主要可能原因是，不能写入可执行文件到 `/usr/bin` 目录

解决方案一

安装可执行文件到 `/usr/local/bin`

`sudo gem install cocoapods -n /usr/local/bin`

解决方案二

```bash
export GEM_HOME=$HOME/.gem
export PATH=$GEM_HOME/bin:$PATH
```

将 GEM 环境变量添加到`.bash_profile`

安装 cocoapods 到用户家目录

```
gem install cocoapods --user-install
```

解决方案三

使用 brew 安装 cocoapods

解决方案四

使用 [RVM](https://github.com/rvm/rvm) Ruby 版本管理器，更多详细 [stackoverflow](https://stackoverflow.com/questions/18599889/error-while-executing-gem-gemfilepermissionerror/25716203)

### 检查安装包

- 查看包安装位置 `gem which cocoapods`

- 查看 CocoaPods 版本 `pod --version`

- 查看 CocoaPods 环境变量 `pod env`

- 其它命令

  - 查看 gem 安装的包 `gem list`

  - 清除缓存未使用的包 `gem cleanup`

  - 使用过程中的常见问题 [Troubleshooting](https://guides.cocoapods.org/using/troubleshooting)






