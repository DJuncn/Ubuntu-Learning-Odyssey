## 【2025-8-26】初入 Ubuntu

这天，我突然想起之前看到的某个博主的 Halo 博客网站还挺好看的，便想着学习搭建一下子，尝试建站。于是，我便去 b 站上看了许多视频教学。按我最初的设想，本来呢是想用 Centos 最原始的一步一步慢慢搭建所有环境的，因为我从 Linux 入门至今使用的都是 Centos 嘛，想着上手会快一点。但我找了很多个视频都没有找到一步步搭建的，全是使用一站式服务，如宝塔、1panel，并且使用的还是 Ubuntu 系统，他们也极力的推荐使用 Ubuntu 系统。

我以前是了解过 Ubuntu 系统的，也知道 Ubuntu 的稳定性非常的好，嵌入式普遍用的也是它。联想到 Centos 也早已停止维护了，学习使用一下 Ubuntu 也挺好。就这样我踏上了 Ubuntu 的学习之路。



## 【2025-8-27】vi 编辑器的退格问题

刚接触 Ubuntu 操作系统，今天修改配置文件时打错了一个字符突然发现我竟然无法使用退格键删除内容，好不容易删掉了，在右移动的时候又出现了问题，突然在上面一行冒出来一个 d，再动一下光标又多一行，我只好不保存退出去重新编辑。我还以为是我操作有误造成的，但是后面又出现了同样的情况，不管怎么使用退格键就是删不了，于是我便换了一个角度，退格键用不了那我用 `dw` 呢，事实证明 `dw` 是可以的，接着又尝试使用 `d+右移动键`，发现也是可以的。但这样下去可不是个事，这用着也忒不方便了，我就开始在网上搜问题，找教程，看博客。就这样我找到了我认为最为合理的原因：

> **方向键：**
>
> 产生这个问题的原因是由于 Ubuntu 预安装的是 **vim-tiny** 版本，缺省设置是 vim.tiny 以兼容 Vi 模式运行，意味着更接近纯 Vi，不能使用小键盘和方向键，在编辑模式下，只能使用 h j k l 来移动光标。所以要么安装 vim 完整版，要么禁用兼容模式。
>
> **退格键：**
>
> 退格键在编辑模式无法使用其实是 Vi 的一个保护性行为，防止误删除或者一次性删除大量内容，并不是 bug，设置 backspace (bs) 可以让退格键重新“工作”起来，默认设置为空 set backspace =“”，意即使用 Vi 兼容的退格方式，也就是向左移动一个字符 (等同于 h)
>
> 附上我看的那篇文章：[Ubuntu 的 Vi 编辑器方向键变 ABCD 以及退格键无法使用的解决办法 | Win Notes (winotes.net)](https://winotes.net/how-to-fix-arrow-key-that-display-abcd-in-vi-on-ubuntu/)

**解决方法：**

方法一：安装 vim

```
sudo apt-get remove vim-common
sudo apt-get install vim
```

方法二：修改配置文件

找到 `set compatible`，将 “`compatible`” 改成 “`nocompatible`” 非兼容模式就可以解决方向键变 ABCD 以及退格键的问题了。
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



