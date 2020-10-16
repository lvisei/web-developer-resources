# Miniconda 搭建

## 安装

官方下载Miniconda： https://docs.conda.io/en/latest/miniconda.html

清华镜像下载Miniconda： https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/

下载 sh

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -O ~/miniconda.sh
```

安装 Miniconda，`- p` 设置安装目录

```bash
bash ~/miniconda.sh -b -p $HOME/miniconda
```

删除下载的 sh

```bash
rm https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
```

设置默认的 `base` 不是 `conda` 启动时候的默认环境，避免环境变量冲突

```bash
conda config --set auto_activate_base false
```



## 设置镜像

清华镜像

```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```

## 删除 Miniconda

```bash
rm -rf $HOME/miniconda
```

编辑 `~/.bash_profile` 删除环境变量

```bash
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/Users/john/DevelopmentSDK/miniconda3/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/Users/john/DevelopmentSDK/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/Users/john/DevelopmentSDK/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/Users/john/DevelopmentSDK/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

删除文件目录

```bash
rm -rf ~/.condarc ~/.conda ~/.continuum
```



## 使用

查看conda版本

```
conda --version
```

更新 conda

```bash
conda update conda
```

创建新的环境，pakages 可以制定版本 ` python=3.7 scipy=0.15.0`

```bash
conda create -n myenv [pakages]
```

激活、进入某个环境

```bash
conda activate myenv
```

退出环境

```bash
conda deactivate
```

复制环境

```bash
conda create --name mycloneenv --clone myenv
```

查看当前的环境列表

```bash
conda env list
```

查看某个环境下安装的库

```bash
conda list -n myenv
```

安装包

```bash
conda install XXX[==0.0.1]
```

更新包

```bash
conda update XXX
```

删除包

```bash
conda remove XXX
```

安装到指定环境

```bash
conda install -n myenv XXX
```