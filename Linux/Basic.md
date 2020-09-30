# Linux 基础

## 关机命令

shutdown, halt, init 0, poweroff

- 指定在早上8：00关机

```shell
shutdown -h 8:00
```

- 指定在三分钟后关机

```shell
shutdown -h +3
```

- 立刻关机

```shell
halt
```

- 重新启动

```shell
reboot
init 6
```

## Shell 命令

Shell命令可以被分为内部(内置builtin)命令和外部命令。

- 内部命令是shell本身包含的一些命令，这些内部命令的代码是整个shell代码的一个组成部分；
- 外部命令的代码则存放在一些二进制的可执行文件或者shell脚本中
- type命令判断是否内部还是外部命令：

```shell
type cd
```

cd 是shell 内建

- 显示shell的路径：

```shell
echo $SHELL
```

- 查看环境变量值：

```shell
set 或 env
```

- 设置环境变量值：
变量名=值

## 命令行输入的语法结构

```shell
command [[-]option(s)] [option argument(s)] [command argument(s)]
```

## 常用命令

- whatis：得到任何LINUX命令的更短的描述命
- whoami：显示用户名
- hostname：显示登录上的主机的名字
- uname：显示关于运行在计算机上的操作系统的信息
- id：显示用户id和组id等信息
- 路径设置  
    PATH=~/bin:$PATH:. 搜索路径中增加~/bin和.目录

创建和显示目录

- pwd print working directory，显示工作目录
- mkdir make directory，创建目录
- rmdir remove directory，删除目录
- cd change directory，改变当前的工作目录

显示文件内容

- cat 同时显示一个或多个文件的内容  
    cat sample 显示sample文件的内容
- more 、pg 一次显示一个屏幕的内容
    more sample phones
一次显示一屏幕的sample文件的内容，然后以同样的方式显示phones文件的内容
- 显示日历命令  
    cal [[month] year]
