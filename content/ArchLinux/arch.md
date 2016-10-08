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
    
    ```
    fdisk /dev/sda 
    ```

### efi启动方式分区 mbr启动启动方式则略过 ###

    如果想efi启动方式，不存在esp分区，首先要创建

    gidsk 创建一个500M 左右空间分区，选择区名 ef00 
    格式化为fat32格式

    ```
    mkfs.fat -F32 /dev/<TEAT_PARTITION> 
    ```

    把esp分区挂载到/boot
    

    ==> 如果存在esp分区,则不用创建,直接执行下面步骤

    首先将 esp uefi引导分区 挂载到/boot

    mount --bind /esp /boot  查看下--bind 参数意思

    是否需要 必须使用root = system_root 内核参数才能通过此方法启动

    https://wiki.archlinux.org/index.php/EFI_System_Partition   参考地址


==================>分区  方案地址
https://wiki.archlinux.org/index.php/Partitioning_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

建议分区大小
/boot  - 200MB    前面分区已经将esp分区挂载到/boot/下
/      - 15-20GB  系统目录，根据软件数量会产生明显增长，如果将swap文件放在这里，需要调大值
/var   - 8-12GB   除了其他数据以外，还包括ABS 树和 pacman 缓存。保留缓存的包提供了包降级的能力.应该在15-20GB 之间
swap   - 4G 足以  缓存空间
/home  - 剩余空间 

==================> swap 分区
https://wiki.archlinux.org/index.php/Swap_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

交换分区可以用fdisk创建，安装linux交换区域

mkswap /dev/sda2

启用设备作为交换区

swapon /dev/sda2

启动时自动启动交换分区，添加条目到 fstab

/dev/sda2 none swap defaults 0 0


==================> 格式化分区 
https://wiki.archlinux.org/index.php/File_systems_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

mkfs -t ext4 /dev/sda 

mkfs.ext4 /dev/sda1 格式化分区

=================>挂载分区到指定文件夹
创建home分区及boot分区，要挂载到/mnt/home 及 /mnt/boot 分区下

首先把 根分区/ 挂载到/mnt

挂载其他分区前，首先在/mnt 下创建 目录, swap不用挂载
mkdir /mnt/boot  
mkdir /mnt/home
mkdir /mnt/var

mount /dev/sda1 /mnt
mount /dev/sda2 /mnt/home


mount /dev/sda1 /mnt 挂载分区到/mnt路径下

vi /etc/pacman.d/mirrorslist 修改源，首先使用这几个国外源，

Server = http://lug.mtu.edu/archlinux/$repo/os/$arch
Server = http://mirror.umd.edu/archlinux/$repo/os/$arch
Server = http://mirrors.liquidweb.com/archlinux/$repo/os/$arch
Server = http://cosmos.cites.illinois.edu/pub/archlinux/$repo/os/$arch

=================>查看一下其他源，

## Score: 2.7, China
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
## Score: 4.0, China
Server = http://mirrors.stuhome.net/archlinux/$repo/os/$arch
## Score: 4.3, China
Server = http://mirror.bjtu.edu.cn/archlinux/$repo/os/$arch
## Score: 6.8, China


由于电脑是uefi启动模式 ，首先检测是否是否有gpt且有esp分区     esp与mbr分区区别，祥见

parted /dev/sda print

==================>连接网络

lspci -k / lsusb -v 检查驱动是否加载

dmesg | grep usbcore 检查usb设备驱动

ip link 查看无线设备名（通常类似wlp2s1)的设备

没有驱动则需安装驱动====== 略过先

iw dev  获取接口名

iw dev waln0 link 检查连接状态

iw dev waln0 station dump  获取统计数据

====> 连接网络 

ip link set wlan0 up / ip link set dev wlan0 up 激活内核接口

ip link show wlan0    验证端口是否打开

iw dev wlan0 scan | less  查看接入点

设置 /etc/wpa_supplicant/wpa_supplicant.conf 

```
network={
	ssid='tenda-8501'
	psk='86080225'
}
```
wpa_supplicant -B -i wlp13s1 -c /etc/wap_supplicant/wpa_supplicant.conf   利用wpa_supplicant连接无线

dhcpcd wlp13s1  获取ip




==================>安装 系统

安装 系统 必须要网络连接（此处需要查看 wifi 连接方法 ）

pacstrap /mnt base


uefi 安装 及 grub 介绍
https://wiki.archlinux.org/index.php/GRUB 


===================================> 此块应该在系统完成后至执行

安装uefi下grub

arch-chroot /mnt /bin/bash

简单方法 将所有文件都放到esp中，在grub-install命令添加 --boot-directory-esp 选项

# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=grub
把esp修改为/boot 路径，安装时缺少efibootmgr时，执行pacman -S efibootmgr 

grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=grub --boot-directory=esp --debug

这个命令会将 GRUB 文件放在 {{ic|esp/grub} 而不是 /boot/grub 中. 使用这个方法, 请确保 grub-mkconfig 设置了正确的目录: 

grub-mkconfig -o esp/grub/grub.cfg







安装 系统 必须要网络连接（此处需要查看 wifi 连接方法 ）

pacstrap /mnt base

安装完毕 

arch-chroot /mnt 

此时安装grub

pacman -S grub --force 

安装grub 到/dev/sda1

grub-install /dev/sda --force

最后配置boot文件

grub-mkconfig -o /boot/grub/grub.cfg



https://wiki.archlinux.org/index.php/GRUB_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
https://wiki.archlinux.org/index.php/Systemd-boot_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)


百度贴吧详细介绍 arch linux 安装教程
http://tieba.baidu.com/p/2884280103



ip addr 查看本机ip

请使用`ip`命令或安装常见软件包
`pacman -S net-tools dnsutils inetutils iproute2`

启动无网络,执行 dhcpcd 即可


安装i3

pacman -S xorg 
pacman -S i3
pacman -S xorg-xinit

pacman -S xterm

linux 可视化终端文件管理器
Midnight Commander



无线网卡问题
https://bbs.archlinux.org/viewtopic.php?id=206831

https://kozikow.com/2016/06/03/installing-and-configuring-arch-linux-on-thinkpad-x1-carbon/#Minimalist-distributions-teach-you-Linux

http://www.zhihu.com/question/24065933

pacman 命令
http://blog.51yip.com/linux/1037.html


历史wap_supplicant
https://git.archlinux.org/svntogit/packages.git/log/trunk?h=packages/wpa_supplicant

https://wiki.archlinux.org/index.php/Kernel_modules_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29

system-networkd
http://www.linuxidc.com/Linux/2015-11/125430.htm

arch linux 软件降级
http://www.linuxfans.org/archives/3193


解释为什么提示 p2p-dev-wlp4s0没有此接口
https://bbs.archlinux.org/viewtopic.php?id=196584


google 搜索 could not read interface p2p-dev-wlp4s0 flags : not found device
