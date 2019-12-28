# 编译Linux-2.6内核
为Linux2.6添加一个系统调用，并编译它看看结果如何
-------------------------------------------------

上周做了操作系统的课程设计，有一个题目是在Linux内核中添加一个系统调用。

很不错的一个题，因为我不太清楚这个系统里面是什么样子的，我们常用的函数和功能的源码在哪。不编一次源码就算各种操作都很娴熟那也只是在操作一个黑盒子。

网上的教程很少，我是根据这一篇文章来的：
<https://www.cnblogs.com/kenjones/archive/2011/03/09/1978611.html>

编译前的环境准备：

到Ubuntu官方网站下载老版本ubuntu 10.10镜像并在VMware上安装这个虚拟机
<http://old-releases.ubuntu.com/releases/>
主要流程为：

下载并解压源码
对源码文件进行修改添加新的系统调用
编译源码得到新内核
配置启动项重启后进入新的内核
测试看系统调用是否成功
以下操作很多涉及root权限，建议先sudo su

## 1 下载并解压源码

到下面的官方网站找到2.6.35.3的源码下载并解压到/usr/src

## 2 对源码文件进行修改添加新的系统调用

路径与添加的内容在下图中
printk是向系统日志中打印字符，在此用它来打印一行歌词

## 3 编译源码得到新内核

make prproper

清除以前进行过的内核功能性选择文件

make clean

清除以前生成的目标文件和其他文件

make oldconfig

采用默认的内核配置

make bzImage

编译内核，大概花费半个小时

make modules

编译模块，大概需要一个半到两个小时

make modules_install

安装模块

## 4 配置启动项重启后进入新的内核

上面的几步做完后，在 /usr/src/linux-2.6.35.3/arch/i386/boot下会有一个编译好的内核bzImage

把该内核复制到/boot下并改名为vmlinuz-2.6.35.3-mykernel(vmlinuz没有拼错哦)

我们还需要一个initrd文件，上面的博文中讲到要用下面的工具但是有一个问题Ubuntu10.10的apt源大部分已经不能用了，要先把sources.list里面的服务器源换掉
我使用的ubuntu官方提供的旧版镜像专用源
然后再 apt-get install bootcd-mkinitramfs

mkinitramfs -o /boot/initrd.img-2.6.35.3

修改引导文件grub.cfg

每个menuentry是一个单位，在原有的两个menuentry上面再添加两个menuentry，并修改这两个menuentry，私以为只要添加一个menuentry就可以。

reboot重启

## 5 测试看系统调用是否成功

dmesg -c是查看系统日志，看一看printk有没有向系统日志中打印那句歌词就好啦

----------------------------------------------------------------------
我后来发现，实际上只编译内核就可以了...
