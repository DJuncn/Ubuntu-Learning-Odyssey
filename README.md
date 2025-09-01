# 学习 Ubuntu 的那些事儿

项目地址：[https://github.com/DJuncn/Ubuntu-Learning-Odyssey](https://github.com/DJuncn/Ubuntu-Learning-Odyssey)

文章地址：[https://djuncn.github.io/Ubuntu-Learning-Odyssey/#/](https://djuncn.github.io/Ubuntu-Learning-Odyssey/#/)



## 【2025-8-26】初入 Ubuntu

这天，我突然想起之前看到的某个博主的 Halo 博客网站还挺好看的，便想着学习搭建一下子，尝试建站。于是，我便去 b 站上看了许多视频教学。按我最初的设想，本来呢是想用 Centos 最原始的一步一步慢慢搭建所有环境的，因为我从 Linux 入门至今使用的都是 Centos 嘛，想着上手会快一点。但我找了很多个视频都没有找到一步步搭建的，全是使用一站式服务，如宝塔、1panel，并且使用的还是 Ubuntu 系统，他们也极力的推荐使用 Ubuntu 系统。

我以前是了解过 Ubuntu 系统的，也知道 Ubuntu 的桌面开发的非常优雅，稳定性好，嵌入式中普遍使用的也是它。联想到 Centos 也早已停止维护了，学习使用一下 Ubuntu 也挺好。就这样我踏上了 Ubuntu 的学习之路。



## 【2025-8-27】vi 编辑器的退格问题

昨天刚接触 Ubuntu 操作系统，今天修改配置文件时打错了一个字符突然发现我竟然无法使用退格键删除内容，好不容易删掉了，在右移动的时候又出现了问题，突然在上面一行冒出来一个 d，再动一下光标又多一行，我只好不保存退出去重新编辑。我还以为是我操作有误造成的，但是后面又出现了同样的情况，不管怎么使用退格键就是删不了，于是我便换了一个角度，退格键用不了那我用 `dw` 呢，事实证明 `dw` 是可以的，接着又尝试使用 `d+右移动键`，发现也是可以的。但这样下去可不是个事，这用着也忒不方便了，我就开始在网上搜问题，找教程，看博客。就这样我找到了我认为最为合理的原因：

> **方向键：**
>
> 产生这个问题的原因是由于 Ubuntu 预安装的是 vim-tiny 版本，缺省设置是 vim.tiny 以兼容 Vi 模式运行，意味着更接近纯 Vi，不能使用小键盘和方向键，在编辑模式下，只能使用 h j k l 来移动光标。所以要么安装 vim 完整版，要么禁用兼容模式。
>
> **退格键：**
>
> 退格键在编辑模式无法使用其实是 Vi 的一个保护性行为，防止误删除或者一次性删除大量内容，并不是 bug，设置 backspace (bs) 可以让退格键重新 "工作" 起来，默认设置为空 set backspace = ""，意即使用 Vi 兼容的退格方式，也就是向左移动一个字符 (等同于 h)
>
> 附上我看的那篇文章：[Ubuntu 的 Vi 编辑器方向键变 ABCD 以及退格键无法使用的解决办法 | Win Notes (winotes.net)](https://winotes.net/how-to-fix-arrow-key-that-display-abcd-in-vi-on-ubuntu/)

**解决方法：**

方法一：安装 vim

```
sudo apt-get remove vim-common
sudo apt-get install vim
```

方法二：修改配置文件

找到 `set compatible`，将 "`compatible`" 改成 "`nocompatible`" 非兼容模式就可以解决方向键变 ABCD 以及退格键的问题了。
这个修改适用于所有用户，如果只想对当前用户生效，需要在用户 Home 目录下面新建 .vimrc 文件，然后加入 `set nocompatible` 一行再保存修改即可。

在某些系统下面，可能会出现退格键还是无法使用的现象，那这个时候得在 `set nocompatible` 后面再加一行：

`set backspace=2` 或者

`set backspace=indent,eol,start`

以上两行二选一，效果一样。

| 值     | 效果                                                         |
| ------ | ------------------------------------------------------------ |
| indent | 允许在自动缩进上退格                                         |
| eol    | 允许在换行符上退格 (连接行)                                  |
| start  | 允许在插入开始的位置上退格；CTRL-W 和 CTRL-U 到达插入开始的位置时停留一次。 |

**完整内容如下：**

```
root@test-virtual-machine:~# vi /etc/vim/vimrc.tiny 
" Vim configuration file, in effect when invoked as "vi". The aim of this
" configuration file is to provide a Vim environment as compatible with the
" original vi as possible. Note that ~/.vimrc configuration files as other
" configuration files in the runtimepath are still sourced.
" When Vim is invoked differently ("vim", "view", "evim", ...) this file is
" _not_ sourced; /etc/vim/vimrc and/or /etc/vim/gvimrc are.

" Debian system-wide default configuration Vim
set runtimepath=~/.vim,/var/lib/vim/addons,/usr/share/vim/vimfiles,/usr/share/vim/vim82,/usr/share/vim/vimfiles/after,/var/lib/vim/addons/after,~/.vim/after

set nocompatible	# 修改此行，原内容为set compatible
set backspace=2		# 添加此行

" vim: set ft=vim:
```

**补充：**

【2025-8-29.1】

前两天由于跟着视频学习的缘故我一直在使用 Ubuntu 的桌面版（ubuntu-22.04.5-desktop-amd64.iso），虽然有桌面使用起来蛮方便的，但我还是习惯使用纯命令行，尽管通过 ssh 远程连接之后会变成纯命令行，但我还是选择安装一台纯命令行的系统。

这我也是有过考量的，首先，在下载 Ubuntu 镜像的时候发现————它有两种版本：ubuntu-22.04.5-desktop-amd64.iso、ubuntu-22.04.5-live-server-amd64.iso；其次，相比于桌面版，服务器版没有安装那么多的软件要更加轻量精简；最后，我想了一下，在生产环境中大多数都是用的服务器的版本，它们两个版本构建的环境肯定会有差别的，所以我觉得使用更接近生产环境的版本更好。

既然已经决定好，我也就着手开始安装了。安装完成之后，我在修改 `/etc/ssh/sshd_config` 文件时突然发现，此时的 vi 编辑器并没有出现上面的情况，且 vim 是已经安装好的。这也更加证实了我的猜想。

【2025-8-29.2】

后面我又重新安装了服务器版，发现在安装过程中有一处选项：1.Ubuntu Server，2.Ubuntu Server (minimized)。在默认选项中使用的是 1。这时我才发觉，原来我选的并不是最小化安装。这次我选择了最小化安装，安装完成后我依旧是准备去修改 `/etc/ssh/sshd_config`，结果连 vi 这个命令都没有，这就很尴尬了 [\自嘲+挠头]。



## 【2025-8-28】通过 netplan 配置文件修改网卡 IP

这两天学习了 Ubuntu，当时学习安装的是桌面版的系统，我跟着教学一步步搭建系统，进入系统后便开始修改网卡 IP，通过桌面点点点输入的方式，很简单非常容易上手。于是我便在想如何通过配置文件来修改 IP 地址呢，因为我往常使用 Centos 的时候都是通过修改配置文件来修改 IP 地址的。毕竟是两个不同的操作系统，我并没有在 Ubuntu 中找到网卡配置文件，我只好再次上网查找解决办法。看了许多篇文章个人感觉这篇文章写的还较为全面：

> **简介**
>
> Ubuntu 18.04 开始，Ubuntu 和 Debian 移除了以前的 `ifup/ifdown` 命令和/etc/network/interfaces 配置文件，转而使用 `ip link set` 或者 `/etc/netplan/01-netcfg.yaml` 模板和 `sudo netplan apply` 命令实现网络管理。
>
> Netplan 是抽象网络配置描述器，用于配置 Linux 网络。 通过 `netplan` 命令，你只需用一个 YAML 文件描述每个网络接口所需配置。`netplan` 并不关心底层管理工具是 NetworkManager 还是 networkd。
>
> netplan 的使用可能导致新版本 Ubuntu 重启后网络无法连通。
>
> 原文链接：[Netplan 使用（高版本 Ubuntu 静态/动态 IP 设置方法）-CSDN 博客](https://blog.csdn.net/bigbaojian/article/details/125396045)

**实现方法：**

桌面版的初始内容：

```
root@test-virtual-machine:~# cat /etc/netplan/01-network-manager-all.yaml
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
```

1.修改配置

```
root@test-virtual-machine:~# vi /etc/netplan/01-network-manager-all.yaml
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:		# 可以ip a查看以太网连接名称
      dhcp4: false     # true,false也可以写成yes,no
      addresses:
        - 192.168.147.200/24
      # gateway4: 10.10.10.1
      # gateway4已经废弃，推荐使用routes
      routes:
        - to: default
          via: 192.168.147.2
      nameservers:
        addresses:
          - 114.114.114.114
```

2.检测文件正确性

```
root@test-virtual-machine:~# netplan try
```

3.应用配置

```
root@test-virtual-machine:~# netplan apply
```

**命令**

完成了配置文件的描述，之后就可以进行通过 `netplan` 通过针对后端管理工具生成配置文件。
`netplan` 操作命令提供常用子命令如下：

- `netplan generate` ：生成后端管理工具对应的配置；
- `netplan apply` ：应用配置，必要时重启管理工具；
- `netplan try` ：在配置得到确认之后才应用，如果配置存在错误，则回滚，类似 test；
- `netplan get`：获取当前 netplan 配置；
- `netplan set`：修改当前 netplan 配置。

`netplan --debug apply` 返回错误信息。

注：网关不确定的情况下，可以通过 `networkctl status` 查看（如果使用的是 VMware 虚拟机，则.2 地址为网关和本地 DNS 服务器）。

**补充：**

【2025-8-29】

在【2025-8-27】vi 编辑器的退格问题那一栏中，我在末尾补充了我改用 Ubuntu 服务器版（ubuntu-22.04.5-live-server-amd64.iso）的原因。在注意到 vi 编辑器的不同之后，我迅速就联想到了网卡配置文件，果不其然，网卡配置文件的初始内容也有所不同。也有可能是我在安装过程中手动配置静态网卡的缘故？仔细一想，好像确实有这个可能。我又重装了一遍，在配置网卡的地方我没去修改它，而是使用默认。安装完成后，我再次 `cat` 查看配置文件内容。

经验证，在安装过程中经过手动配置网卡后，配置文件里面的内容确实会多出更多的内容。那么这个时候在安装过程中手动配置网卡的好处就凸现出来了，当你下一次修改网卡便不用再去输入 "名词" 了，直接在里面修改 IP 地址就可以了。

服务器版的初始内容（手动）：

```
root@ubuntuserver:~# cat /etc/netplan/50-cloud-init.yaml 
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        ens33:
            addresses:
            - 192.168.147.210/24
            nameservers:
                addresses:
                - 114.114.114.114
                search: []
            routes:
            -   to: default
                via: 192.168.147.2
    version: 2
```

服务器版的初始内容（自动）：

```
root@ubuntuserver:~# cat /etc/netplan/50-cloud-init.yaml 
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        ens33:
            dhcp4: true
    version: 2
```



## 【2025-8-30】安装 Docker 和 Docker Compose

**官方地址**

- 安装 Docker：[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)


- 安装 Docker Compose：[https://docs.docker.com/compose/install/standalone/](https://docs.docker.com/compose/install/standalone/)

今天，花了一下午研究了一下 Ubuntu 是如何安装 Docker 和 Docker Compose。

总的来说还是受益匪浅的。

通过阅读官方文档以及向 AI 提问，我将流程梳理总结了一下，目前发布在了 csdn 上面。

博客地址：[Ubuntu 安装 Docker 和 Docker Compose-CSDN 博客](https://blog.csdn.net/Jun__Deng/article/details/151019067?spm=1001.2014.3001.5501)



## 【2025-8-30】Ubuntu 服务器版安装步骤

使用镜像：ubuntu-22.04.5-live-server-amd64.iso

在研究完 Docker 后，我想着再熟悉一下 Ubuntu 服务器版安装步骤，于是便顺手将步骤给截图保存了下来。既然保存了下来也不能浪费，刚好 csdn 有个每日任务————发布两篇博客有一张 1500 曝光的流量券。整理了一下也顺手给发布了。

博客地址：[Ubuntu 服务器版安装步骤-CSDN 博客](https://blog.csdn.net/Jun__Deng/article/details/151019537?spm=1001.2014.3001.5501)



## 【2025-8-31】搭建 Cloudreve 私人网盘

> **Cloudreve 是什么？**
>
> Cloudreve 可以让您快速搭建起公私兼备的网盘系统。Cloudreve 在底层支持不同的云存储平台，用户在实际使用时无须关心物理存储方式。你可以使用 Cloudreve 搭建个人用网盘、文件分享系统，亦或是针对大小团体的公有云系统。
>
> Cloudreve 本身是一款“网盘”而非“其他网盘的管理器”，其所支持的存储提供商大多为公有云的存储专用产品，而非针对个人消费者的网盘。下面情况 **并不适合** 使用 Cloudreve：
>
> - 我需要用 Cloudreve 管理多个网盘上的文件；
> - 对于 Cloudreve 存储的文件，我同时也会使用其他文件管理器一起管理；
> - 我想把其他网盘存储服务“再分发”给我的用户使用；



今天抽空看了一下 Cloudreve 的文档。

官网给的 docker-compose.yml 文件是以 `PostgreSQL`，我稍微改了一下，改成了 `MySQL`。既然数据库改了，那变量配置自然也是要改的。改为之后问题就出现，cloudreve-backend 一直处于重启状态，很明显，这个容器没有起来。没有起来那就去看日志，提示数据连接失败，大概率是密码出现了问题，主要问题还是出现在变量上面，毕竟我就改了变量。锁定了方向那就好办了，直接去检查 MySQL 变量，仔仔细细看了两三遍发现变量没错，这就有点意思了，我又转过去研究官网文档。这一看，还真看到了问题所在————PostgreSQL 有个允许用户无需密码即可连接数据库的变量 `POSTGRES_HOST_AUTH_METHOD=trust`，所以在 cloudreve-backend 中并没有添加数据库的密码变量。当我换成 MySQL 后，就需要在 cloudreve-backend 中多添加一行密码变量。

在 cloudreve-backend 中加上变量后也，容器也是成功 up 运行起来了。

后面又在 docker-compose.yml 里面增加 Nginx 的配置，给 Cloudreve 配置上了反向代理。

花了一点时间总结成了文档，目前还没有发布到博客上。后面等我发布了会补上链接的。
