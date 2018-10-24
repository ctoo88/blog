## ArchLinux安装指南

2018/10/24

本文内容主要参照 [Arch Wiki 官方指南](https://wiki.archlinux.org/index.php/Installation_guide)

##### 准备工作

下载ISO镜像文件 [官方地址](https://www.archlinux.org/download/)

制作USB启动盘，windows下推荐 [USBwriter](https://sourceforge.net/projects/usbwriter/files/latest/download)，linux下有更简便的方法，详见[USB flash installation media](https://wiki.archlinux.org/index.php/USB_flash_installation_media)

##### 检查安装盘

插上USB开机，按F2或F10等键(不同的电脑有所不同)，进入启动设置。选择从“USB”启动。

*如果成功就能看到ArchLinux安装界面了*

选择“Boot Arch Linux (x86_64)”选项

*经过各种系统检查后，Arch Linux 会启动到 root 用户的命令行界面*

    root@archiso ~ #

*如果一切顺利，说明安装盘没有问题了，接下来就是安装系统了*

##### 联网

连有线请参考[Dhcpcd](https://wiki.archlinux.org/index.php/Dhcpcd)，
连无线请参考[Netctl](https://wiki.archlinux.org/index.php/Netctl)。本文使用无线

连接wifi命令

    # wifi-menu

测试网络

    # ping www.baidu.com

##### 选择镜像

    # vim /etc/pacman.d/mirrorlist

执行`/`命令搜索“China”把中国的镜像移动到文件顶部

##### 分区

UEFI/BIOS 检测

    # ls /sys/firmware/efi

如果提示`no such file or directory: /sys/firmware/efi`则是BIOS模式，否则是UEFI模式。

分区工具推荐 [fdisk](https://wiki.archlinux.org/index.php/Fdisk)

    # fdisk /dev/sda

进去后按`m`查看帮助

通常BIOS模式用`o`命令建分区表，UEFI模式用`g`命令，然后用`n`命令添加分区。完成后用`W`命令保存并退出。

##### 格式化分区

    # mkfs.ext4 /dev/sda1

##### 挂载分区

    # mount /dev/sda1 /mnt

##### 安装系统

安装基本包

    # pacstrap /mnt base

##### 配置系统

配置fstab文件

    # genfstab -U /mnt >> /mnt/etc/fstab

检查一下

    # cat /mnt/etc/fstab

切换到新系统

    # arch-chroot /mnt

 
root密码

    # passwd

Intel CPU

    # pacman -S intel-ucode
    
安装Bootloader，参考[GRUB](https://wiki.archlinux.org/index.php/GRUB)

    # pacman -S grub
    # grub-install --target=i386-pc /dev/sda
    # grub-mkconfig -o /boot/grub/grub.cfg
    
    
注：笔记本用无线一定要执行下面命令，安装必要模块

    # pacman -S iw wpa_supplicant dialog

##### 重启

    # exit
    # reboot    

##### 完善系统

时区

参考[Time](https://wiki.archlinux.org/index.php/Time#Time_zone)

    # cd /usr/share/zoneinfo/
    # ln -sf Asia/Shanghai /etc/localtime
    
设置硬件时间(UTC)

    # hwclock --systohc

本地语言

    # vi /etc/locale.gen

执行`/`命令找到

    en_US.UTF-8 UTF-8
    zh_CN.UTF-8 UTF-8

删除这两行前面的`#`，然后执行下面的命令

    # locale-gen
    # echo LANG=en_US.UTF-8 > /etc/locale.conf

网络配置

hostname

    # echo myhostname > /etc/hostname

hosts

    vi /etc/hosts
    
hosts内容

    127.0.0.1	localhost
    ::1		localhost
    127.0.1.1	myhostname.localdomain	myhostname

##### 安装显卡驱动

参考[Intel](https://wiki.archlinux.org/index.php/Intel_graphics)

    # pacman -S xf86-video-intel

##### 安装桌面环境

参考[Xorg](https://wiki.archlinux.org/index.php/Xorg)、[Xfce](https://wiki.archlinux.org/index.php/Xfce)

    # pacman -S xorg-server
    # pacman -S xfce4

##### 禁用独显驱动

vim /etc/modprobe.d/nouveau_blacklist.conf

    blacklist nouveau

重启电脑，检查生效

    lsmod | grep nouveau
    
若没有任何输出表示屏蔽好了
