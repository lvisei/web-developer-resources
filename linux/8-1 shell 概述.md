## 8-1 shell 概述

### Shell是什么
Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用Shell来启动、挂起、停止甚至时编写一些程序。Shell还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。Shell是解释执行的脚本语言，在Shell中可以直接调用Linux系统命令。

### Shell分类
- `Bourne Shell`：主文件名为sh
- `C Shell`: BSD版的Unix
- 两种语法类型有 Bourne 和 C ，彼此不兼容。Bourne家族主要包括 `sh, ksh, Bash( Linux 标准 Shell), psh ,zsh `; C 家族主要包括：`csh, tcsh`
- 其他
   - `echo $SHELL` 查看Shell
   - Bash 与 sh 兼容，现在使用的Linux就是使用 Bash 作为用户的基本 Shell `vi /etc/shells`