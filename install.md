# 安装基本系统

### 下载
* 从[gentoo官网](https://gentoo.org/downloads/)下载iso，我这里下载的是amd64下的Minimal Installation CD
* 光盘启动，
* 网络链接
ping www.baidu.com，如果能ping通，直接下一步
通过ifconfig查看网卡名字，这里是enp3s0
然后执行
```sh
ifconfig enp3s0 192.168.1.168 broadcast 192.168.1.255 netmask 255.255.255.0
ip route add via 192.168.1.1 dev enp3s0
```
vim /etc/resolv.conf
nameserver 114.114.114.114
* 我这里为了安装方便，通过ssh安装
* 启动sshd，
设置root密码，
然后远程连接

#gpt分区

parted -a optimal /dev/sda

mklabel gptprint //输出rm 分区号码 //删除分区，如果不需要可以不做

unit mib

mkpart primary 1 3

name 1 grub

set 1 bios_grub on

mkpart primary 3 131

name 2 boot

mkpart primary 131 643

name 3 swap

mkpart primary 643 -1

name 4 rootfsset 2 boot on  (UEFI主板才需要)quit


格式化
mkfs.ext2 /dev/sda2
mkfs.ext4 /dev/sda4
mkswap /dev/sda3
swapon /dev/sda3

挂载

mount /dev/sda4 /mnt/gentoo

mkdir /mnt/gentoo/boot

mount /dev/sda2 /mnt/gentoo/boot如果/tmp或/var/tmp是独立分区，则在挂载后执行chmod 1777 /mnt/gentoo/tmp
date MMDDhhmmYYYY (Month, Day, hour, minute and Year)

cd /mnt/gentoo下载对应的stage3包

tar xvjpf stage3-*.tar.bz2 --xattrs

查看CPU类型cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -cnano -w /mnt/gentoo/etc/portage/make.conf里

CFLAGS添加-march=native

MAKEOPTS="-j3"

LINGUAS="zh_CN en"

VIDEO_CARDS="intel i965"     //笔记本是radeon r600，参考https://wiki.gentoo.org/wiki/Xorg/Guide

INPUT_DEVICES="evdev synaptics"     //触摸板


cp -L /etc/resolv.conf /mnt/gentoo/etc/#--make-rslave在等下安装systemd支持的时候需要mount -t proc proc /mnt/gentoo/procmount --rbind /sys /mnt/gentoo/sys#mount --make-rslave /mnt/gentoo/sysmount --rbind /dev /mnt/gentoo/dev#mount --make-rslave /mnt/gentoo/dev

chroot /mnt/gentoo /bin/bash

source /etc/profile

export PS1="(chroot) $PS1"

emerge-webrsync

emerge --sync [--quiet]

emerge -av vim
eselect vi list
eselect editor list
. /etc/profile

//通过eselect news list查看news,以下都是news里的
如果/和/usr在不同分区，必须使用initramfs
在package.use里加入下面这行，这样会自动安装32位类库
*/* abi_x86_32     #暂时不加，编译太漫长
在make.conf里加入
PYTHON_TARGETS="python2_7 python3_4"     #emerge --info | grep ^USE可以看到默认已有


在make.conf里设置(USE后)
CPU_FLAGS_X86="${USE}" //不能带-，比如-kde就会报错


eselect profile list

eselect profile set 3 (desktop那个)
vim /etc/portage/make.confemerge --info | grep ^USE //查看当前的USE设置

#USE="mmx mmxext sse sse2 sse3 dvd alsa cdr udev consolekit python pulseaudio git subversion gnome-keyring bash-completion vim-syntax tk"     //alsa是高级音频,cdr是刻录

USE="sse sse2 sse3 python pulseaudio git subversion gnome-keyring bash-completion vim-syntax tk"     //alsa是高级音频,cdr是刻录


echo "Asia/Shanghai" > /etc/timezone

emerge --config sys-libs/timezone-data//时间可能不对，查看下是否需要设置

vim /etc/locale.gen增加

en_US ISO-8859-1

en_US.UTF-8 UTF-8

zh_CN.UTF-8 UTF-8 zh_CN.GB2312 GB2312 zh_CN.GBK GBKzh_CN GB18030


locale-gen
eselect locale list

eselect locale set 10 （选择zh_CN.utf8）
. /etc/profile

vim /etc/env.d/02locale 增加

LC_COLLATE="C"

然后env-update && source /etc/profile
安装

emerge -av gentoo-sources pciutils genkernel

手动安装内核请参考 http://www.gentoo.org/doc/en/handbook/handbook-amd64.xml?part=1&chap=7

cd /usr/src/linux
#编译内核，有两个方法，采用的是第二个方法，配置参考https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation#Installing_the_sources
1、分步进行
make menuconfig     //配置
make && make modules_install
make installgenkernel --install initramfs2、一次执行
//一般配置参考https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation#Installing_the_sources
需要先设置/etc/fstab
emerge --ask radeon-ucode     //笔记本是AMD/ATI显卡，需要

genkernel --menuconfig all     //没什么设置，可以直接genkernel all

#make menuconfig //配置，也可以在genkernel里增加--menuconfig参数进行配置

#genkernel all （--menuconfig要配置内核，--lvm使用LVM2，好像非常慢）

挂载vim /etc/fstab

/dev/sda2     /boot        ext2     defaults,noatime     0 2
/dev/sda3     none           swap      sw                0 0
/dev/sda4     /            ext4     noatime              0 1
/dev/cdrom    /mnt/cdrom   auto     noauto,user          0 0

vim /etc/conf.d/hostname

emerge --noreplace netifrcvim /etc/conf.d/net   (可以配置多个网卡，参考手册)   修改如下(把eth0改成自己的网卡名，可以通过ifconfig查看,比如我的是enp4s0)
//上三行是固定ip，第四行是动态ip，选择一个就可以了

config_eth0="192.168.1.173 netmask 255.255.255.0 brd 192.168.1.255"
routes_eth0="default via 192.168.1.1"
dns_servers_enp4s0="114.114.114.114 223.5.5.5 192.168.1.1"     //指定dns
config_eth0="dhcp"

//如果是多ip，第一个改成

config_eth0="192.168.1.173 netmask 255.255.255.0 brd 192.168.1.255

192.168.1.174 netmask 255.255.255.0 brd 192.168.1.255

192.168.1.175 netmask 255.255.255.0 brd 192.168.1.255"


//设置自启动(把eth0改成自己的网卡名，可以通过ifconfig查看,比如我的是enp4s0)cd /etc/init.dln -s net.lo net.eth0rc-update add net.eth0 default

passwd (修改root密码，一定要做，因为这里的修改跟开始的修改不是同个地方)


emerge -av syslog-ng cronie mlocate dhcpcd#普通用户crontab -l报'/var/spool/cron/crontabs' is not a directory, bailing out.chmod o+rx /var/spool/cron

rc-update add syslog-ng defaultrc-update add cronie defaultrc-update add sshd defaultrc-update add dhcpcd default


emerge -av sys-boot/grub os-prober //os-prober是多系统的时候有用
vim /etc/default/grub把超时时间改成3秒 // 可不做
grub2-install /dev/sda (/dev/sda是启动分区所在)
grub2-mkconfig -o /boot/grub/grub.cfg
exit
cd
umount -l /mnt/gentoo/dev{/shm,/pts,}
umount -l /mnt/gentoo{/boot,/proc,}
shutdown -h now
