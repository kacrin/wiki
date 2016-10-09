---
title: "ArchLinux"
layout: page
date: 2016-10-8 01:00
---

# ArchLinux Install #

## 首先安装arch到u盘 ##

arch安装  iso u盘安装
选择行x86_64 启动

### 查看网络 硬件 ###

首先查看，是否连接网络成功，ping www.baidu.com   ==>arch wiki

lsblk 查看硬件设备

### 磁盘分区 ###
    
    fdisk /dev/sda 

### efi启动方式分区 mbr启动启动方式则略过 ###

如果想efi启动方式，不存在esp分区，首先要创建

gidsk 创建一个500M 左右空间分区，选择区名 ef00 
格式化为fat32格式

    mkfs.fat -F32 /dev/<TEAT_PARTITION> 

把esp分区挂载到/boot
    
==> 如果存在esp分区,则不用创建,直接执行下面步骤

由于电脑是uefi启动模式 ，首先检测是否是否有gpt且有esp分区     esp与mbr分区区别，祥见

```
parted /dev/sda print
```

首先将 esp uefi引导分区 挂载到/boot
查看下--bind 参数意思

```
mount --bind /esp /boot 
```

是否需要 必须使用root = system_root 内核参数才能通过此方法启动

[efi启动参考地址](https://wiki.archlinux.org/index.php/EFI_System_Partition)

## 分区执行方法及方案 ##

### 分区方案 ###

[arch中系统分区方案](https://wiki.archlinux.org/index.php/Partitioning)

建议分区大小

```
/boot  - 200MB    前面分区已经将esp分区挂载到/boot/下
/      - 15-20GB  系统目录，根据软件数量会产生明显增长，如果将swap文件放在这里，需要调大值
/var   - 8-12GB   除了其他数据以外，还包括ABS 树和 pacman 缓存。保留缓存的包提供了包降级的能力.应该在15-20GB 之间
swap   - 4G 足以  缓存空间
/home  - 剩余空间 
```

===> swap 分区

[arch介绍swap分区](https://wiki.archlinux.org/index.php/Swap)

交换分区可以用fdisk创建，安装linux交换区域

```
mkswap /dev/sda2
```
启用设备作为交换区
```
swapon /dev/sda2
```
启动时自动启动交换分区，添加条目到 fstab
```
/dev/sda2 none swap defaults 0 0
```

### 格式化分区 ###

[arch格式化分区](https://wiki.archlinux.org/index.php/File_systems)

两种执行方法
```
mkfs -t ext4 /dev/sda 
mkfs.ext4 /dev/sda1 
```

### 挂载分区 ###

创建home分区及boot分区，要挂载到/mnt/home 及 /mnt/boot 分区下

首先把 根分区/ 挂载到/mnt

挂载其他分区前，首先在/mnt 下创建 目录, swap不用挂载
挂载要根据自己分区的大小以及原先设想好的分区来挂载

```
mkdir /mnt/boot
mkdir /mnt/home
mkdir /mnt/var

mount /dev/sda1 /mnt/boot
mount /dev/sda2 /mnt
mount /dev/sda3 /mnt/home
mount /dev/sda4 /mnt/var
```

## 连接网络 更改源 ##

### 修改安装源 ###

修改源，首先使用这几个国外源，

```
vi /etc/pacman.d/mirrorslist 
```

可以直接寻找mirrorslist里所有China源,放到开头
    
其他源
    
```
Server = http://lug.mtu.edu/archlinux/$repo/os/$arch
Server = http://mirror.umd.edu/archlinux/$repo/os/$arch
Server = http://mirrors.liquidweb.com/archlinux/$repo/os/$arch
Server = http://cosmos.cites.illinois.edu/pub/archlinux/$repo/os/$arch

Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.stuhome.net/archlinux/$repo/os/$arch
Server = http://mirror.bjtu.edu.cn/archlinux/$repo/os/$arch
```

### 检查驱动 网络状态 ###

检查驱动是否加载
```
lspci -k / lsusb -v 
```
检查usb设备驱动
```
dmesg | grep usbcore 
```
查看无线设备名（通常类似wlp2s1)的设备
```
ip link 
```

==> 没有驱动则需安装驱动====== 略过先,系统本身带网卡驱动

获取接口名
```
iw dev
```
检查连接状态
```
iw dev waln0 link 
```
获取统计数据
```
iw dev waln0 station dump
```

### 连接网络 ### 
    
激活内核接口,wlan0为网卡名称
```
ip link set wlan0 up / ip link set dev wlan0 up 
```

验证端口是否打开
```
ip link show wlan0
```

查看接入点
```
iw dev wlan0 scan | less
```

设置 /etc/wpa_supplicant/wpa_supplicant.conf 

```
network={
 ssid='tenda-8501'
 psk='86080225'
}
```
    
* wap_supplicant最新版本bug无法连接到网络,软件降级到低版本
利用wpa_supplicant连接无线
```
wpa_supplicant -B -i wlp13s1 -c /etc/wap_supplicant/wpa_supplicant.conf
```

获取ip
```
dhcpcd wlp13s1
```

## 安装 系统 ##

### 系统安装 ###

安装 系统 必须要网络连接（此处需要查看 wifi 连接方法 ）

开始安装系统,执行
```
pacstrap /mnt base
```

安装完毕,进入系统
```
arch-chroot /mnt 
```

此时安装grub
```
pacman -S grub --force 
```

### mbr启动方式安装grub ###

安装grub 到/dev/sda1
```
grub-install /dev/sda --force
```

最后配置boot文件
```
grub-mkconfig -o /boot/grub/grub.cfg
```

### uefi 安装 及 grub 介绍 ###

[arch grub安装](https://wiki.archlinux.org/index.php/GRUB)

安装uefi下grub
```
arch-chroot /mnt /bin/bash
```

简单方法 将所有文件都放到esp中，在grub-install命令添加 --boot-directory-esp 选项
```
grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=grub
```

把esp修改为/boot 路径，安装时缺少efibootmgr时，执行
```
pacman -S efibootmgr 
```

这个命令会将 GRUB 文件放在 {{ic|esp/grub} 而不是 /boot/grub 中. 使用这个方法, 请确保 grub-mkconfig 设置了正确的目录: 

```
grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=grub --boot-directory=esp --debug
```

更新配置grub.cfg文件
```
grub-mkconfig -o esp/grub/grub.cfg
```

[arch grub](https://wiki.archlinux.org/index.php/GRUBD)
[arch system-boot](https://wiki.archlinux.org/index.php/Systemd-boot)

[百度贴吧详细介绍 arch linux 安装教程](http://tieba.baidu.com/p/2884280103)

## 其他软件安装 ##

### 网络软件 ###

ip addr 查看本机ip

请使用`ip`命令或安装常见软件包
```
pacman -S net-tools dnsutils inetutils iproute2
```

启动无网络,执行 dhcpcd 即可


### i3-wm ###

```
pacman -S xorg 
pacman -S i3
pacman -S xorg-xinit

pacman -S xterm
```

### linux 可视化终端文件管理器 ###

Midnight Commander


### 无线网卡问题 ###
[https://bbs.archlinux.org/viewtopic.php?id=206831](https://bbs.archlinux.org/viewtopic.php?id=206831)
[https://kozikow.com/2016/06/03/installing-and-configuring-arch-linux-on-thinkpad-x1-carbon/#Minimalist-distributions-teach-you-Linux](https://kozikow.com/2016/06/03/installing-and-configuring-arch-linux-on-thinkpad-x1-carbon/#Minimalist-distributions-teach-you-Linux)
[http://www.zhihu.com/question/24065933](http://www.zhihu.com/question/24065933)


### pacman 命令 ###
[http://blog.51yip.com/linux/1037.html](http://blog.51yip.com/linux/1037.html)

### arch linux 软件降级 ###
[http://www.linuxfans.org/archives/3193](http://www.linuxfans.org/archives/3193)

### 历史版本 wap_supplicant ###
利用软件降级方法,把wpa_supplicant还原到以前2.3版本,可以解决无线连接问题
或者直接使用netctl软件无线连接
[https://git.archlinux.org/svntogit/packages.git/log/trunk?h=packages/wpa_supplicant](https://git.archlinux.org/svntogit/packages.git/log/trunk?h=packages/wpa_supplicant)
[https://wiki.archlinux.org/index.php/Kernel_modules](https://wiki.archlinux.org/index.php/Kernel_modules)

system-networkd
http://www.linuxidc.com/Linux/2015-11/125430.htm

解释为什么提示 p2p-dev-wlp4s0没有此接口
https://bbs.archlinux.org/viewtopic.php?id=196584


## 软件版本降级方法 ##

### 参考网址 ###
[http://haochen.info/blog/?p=446](http://haochen.info/blog/?p=446)
[http://www.linuxfans.org/archives/3193](http://www.linuxfans.org/archives/3193)

### 降级 makepkg ###
[makepkg内容](https://wiki.archlinux.org/index.php/Makepkg)

就是软件包使用的上游源码还在。不过在这之前，首先得找到PKGBUILD。
访问[http://archlinux.org](http://archlinux.org)，右侧Package Search中填入wpa_supplicant，选择目标架构（Arch）对应的包——这里我选的是x86_64，会进入到https://www.archlinux.org/packages/core/x86_64/wpa_supplicant/这个页面。 在页面右侧Package Actions中，点Source Files，我们来到了对应包的项目页面： https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/wpa_supplicant。

ArchLinux这个库中，对于每个分支，有trunk和repos两个文件夹。刚才进入的是trunk，这个不是发布到源中的包用的打包脚本。
所以找到summary refs log…下面的path: root/trunk，点root然后进入root/repos，
即这个页面：https://projects.archlinux.org/svntogit/packages.git/tree/repos?h=packages/wpa_supplicant。

core-x86_64文件夹就是我们需要的东西了。点它旁边的log，查看所有和这个文件夹有关的git历史。

ArchLinux包维护是testing测好了之后发布到core/extra/community。15 hours那个包就是我们现在装的，有问题。所以我们降到2014-10-19的包。点那个commit的commit message，进入https://projects.archlinux.org/svntogit/packages.git/commit/repos/core-x86_64?h=packages/wpa_supplicant&id=4ed8733c9b86bcecf6131386a54f1cdec56e9222。

download  packages-4ed8733c9b86bcecf6131386a54f1cdec56e9222.tar.gz

点链接下载，解压，进入packages-4ed8733c9b86bcecf6131386a54f1cdec56e9222/repos/core-x86_64目录，以非root身份运行： 
```
makepkg
```

提示缺依赖的话，用makepkg -s命令。

如果一切顺利，makepkg会从PKGBUILD中下载指定的源码包，然后自动打包生成wpa_supplicant-2.3-1-x86_64.pkg.tar.xz，留在这个目录下。

切换到root，执行

```
pacman -U wpa_supplicant-2.3-1-x86_64.pkg.tar.xz
```


### 禁止某些包自动升级 ###

```
sudo vim /etc/rc.conf
```
添加行
```
IgnorePkg = eclipse nvidia nvidia-utils nvidia-cg-toolkit
```

这样子,我们就可以禁止上面的四个包自动升级了.如果有其它的包想禁止,直接添加就可以了,记住分隔符要用空格哦.

## 校正时间 ##

照wiki上的说
```
#ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
#hwclock --systohc --utc
```
之后你会发现时间慢了8小时因为我们是+8时区的，如果timedatectl设置时间那BIOS里时间就会快8小时。我后面发现只要#nano /etc/locatime 然后把最后一个CST-8改成CST-0就妥妥的了。。。




