---
layout: post
title: ArchLinux 安装经过
description: ""
category: linux
tags: [linux]
---
{% include JB/setup %}
因为实在受不了Ubuntu的更新繁琐，终于还是下定决心安装ArchLinux了。虽说安装起来要比Ubuntu的无脑安装要艰辛许多，
可是成功之后的除了用起来爽很多之外，这其中的过程也是很有意义的。Arch的好处嘛，除了更新方便之外，源也比Ubuntu的
要快很多，社区服务用起来也比Ubuntu更加方便。废话不多说，直接说安装过程。

##准备工作
当然首先是从arch的Download上下载iso文件，然后把它写到U盘中用于U盘引导安装，关于U盘引导，请参照[USB Installation Media](https://wiki.archlinux.org/index.php/USB_Installation_Media)。

我下的是2012.08.04的iso，刻录U盘的软件使用的是Linux Live USB Creator，上述页面中的其他的好像都不能正确引导，所以我还是推荐这个。

<!--more-->

##基本安装
能够正确引导的话就可以进行安装了，安装的时候我完全是照着Wiki上的[Beginner's Guide](https://wiki.archlinux.org/index.php/Beginners'_Guide)一步步过来的。
只是有几点需要注意：
* 因为在开始安装基础包的时候需要网络链接，在网络连接的时候我采用的是static IP的有线连接，在设置网络的时候只需要正确设置有线连接即可。
* 在设置硬盘的时候一定要小心，一般情况下用于安装arch的硬盘已经排到了sda4以后，不要照着Beginner's Guide上面敲sda1等，稍不注意就会把windows给格掉。
* 在设置mirrorlist时，建议把China的几个server单独选出作为新的mirrorlist文件,这样比较稳定。
* 在Configure BootLoader时，我用的是GRUB，同时要安装os-prober,这样能够自动检测其他系统然后会为这些系统建立引导项。命令也就几行:

        # pacman -S grub-bios
        # grub-install --target=i386-pc --recheck /dev/sda
        # cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
        # pacman -S os-prober
        # grub-mkconfig -o /boot/grub/grub.cfg

在注意以下几点之后，就能完成基本的安装了。能够有一个命令行下的基本archlinux系统了。

##扩展安装
在完成基本安装之后，我安装了以下扩展。

###增加用户
这个只需要用`useradd`命令即可。

###安装sudo
通过pacman进行简单安装，然后通过visudo将需要的用户添加到sudolist中。

###安装Gnome3
首先是安装必备的几个包:

    # pacman -S xorg-server xorg-xinit xorg-server-utils

然后安装显卡驱动，我的显卡是nvida的，选用的驱动是nouveau：

    # pacman -S xf86-video-nouveau

然后安装gnome,dbus,gdm:

    # pacman -S gnome, dbus, gdm
同时将dbus，gdm添加到/etc/rc.conf的daemons数组中。

然后安装gnome-tweak-tool用来调节gnome的主题和字体等:

    # pacman -S gnome-tweak-tool

###设置中文和字体
这个部分用来设置archlinux的中文支持和中文字体。

####设置locale
修改/etc/locale.gen文件，将以下行的注释去掉:

    en_US.UTF8 UTF-8
    zh_CN.UTF8 UTF-8
    zh_CN.GBK GBK
    zh_CN.GB2312 GB2312
    zh_CN.GB18030 GB18030

同时在/etc/rc.conf中加入：

    LOCALE=en_US.UTF-8

然后在~/.xprofile中加入：

    export LANG=zh_CN.UTF-8
    export LC_ALL="zh_CN.UTF-8"

####设置中文字体：
安装以下字体包：

    # pacman -S wqy-bitmapfont wqy-zenhei ttf-arphic-ukai ttf-arphic-uming ttf-fireflysung

在gnome的高级设置中设置字体:我把所有的字体都设为文泉驿等宽正黑，这种字体中文和英文都比较圆滑。

###设置无线网络：
首先安装networkmanager，然后按照wiki上的[NetWorkManager](https://wiki.archlinux.org/index.php/NetworkManager)进行设置/etc/rc.conf。

添加networkmanager到daemons数组，同时删除network。

    DAEMONS=(syslog-ng crond dbus networkmanager sshd gdm)

注释interface那一行：

    #interface=eth0

然后安装network-manager-applet，这样就能在gnome启动的时候打开NetWorkManager，可以通过NetWorkManager管理无线网络和有线网络了，
就跟在Ubuntu中一样。

##总结
至此，我安装了我现在archlinux的主要部分，过程虽然比较复杂，但是只要照着wiki来，就能正确完成。
