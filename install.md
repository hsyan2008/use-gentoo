# 安装基本系统
##强烈建议对照[官方文档](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation)
* 从[gentoo官网](https://gentoo.org/downloads/)下载iso，我这里下载的是amd64下的Minimal Installation CD
* 光盘启动
* 网络链接

        ping -c 1 www.baidu.com
    如果能ping通，直接下一步，否则通过ifconfig查看网卡名字，这里是enp3s0，然后执行
    
        ifconfig enp3s0 192.168.1.168 broadcast 192.168.1.255 netmask 255.255.255.0
        ip route add via 192.168.1.1 dev enp3s0
        echo "nameserver 114.114.114.114" > /etc/resolv.conf
* 我这里为了安装方便，通过ssh安装
    * 启动ssh服务
    
            /etc/init.d/sshd start
    * 修改root密码
    
            passwd
* 从其他电脑通过ssh连接过来
* gpt分区（非UEFI主板，双斜杠后面的内容是说明，不要输入！）

        parted -a optimal /dev/sda
        (parted) mklabel gpt
        (parted) print
        (parted) rm 分区号码        //删除分区，如果不需要可以不做
        (parted) unit mib
        (parted) mkpart primary 1 3     //固定为2M的bios启动分区
        (parted) name 1 grub
        (parted) set 1 bios_grub on
        (parted) mkpart primary 3 131       //boot分区
        (parted) name 2 boot
        (parted) mkpart primary 131 643     //交换分区
        (parted) name 3 swap
        (parted) mkpart primary 643 -1      //简单起见，剩下的全部在/分区，实际中可以把home、var等分区独立出来
        (parted) name 4 rootfs
        (parted) quit
* 格式化分区
        
        mkfs.ext2 /dev/sda2
        mkfs.ext4 /dev/sda4
        mkswap /dev/sda3
        swapon /dev/sda3
* 挂载分区

        mount /dev/sda4 /mnt/gentoo
        mkdir /mnt/gentoo/boot
        mount /dev/sda2 /mnt/gentoo/boot
* 修改时间，注意参数要替换成具体数字(Month, Day, hour, minute and Year)

        date MMDDhhmmYYYY
* 安装stage3

        cd /mnt/gentoo
    去[gentoo官网](https://www.gentoo.org/downloads/)下载对应的stage3包

        tar xvjpf stage3-*.tar.bz2 --xattrs
* 配置make.conf
    查看CPU类型
    
        cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
    在/mnt/gentoo/etc/portage/make.conf里配置

        CFLAGS="-march=core2 -O2 -pipe"
        CXXFLAGS="${CFLAGS}"
        MAKEOPTS="-j3"          //cpu核数+1=3
        LINGUAS="zh_CN en"      //安装软件包的时候，如果有中文语言包，就顺便装上
        VIDEO_CARDS="intel i965"     //这是我台式机的配置，笔记本是radeon r600，参考[Xorg/Guide](https://wiki.gentoo.org/wiki/Xorg/Guide)
        INPUT_DEVICES="evdev synaptics"     //synaptics是触摸板
* 拷贝DNS信息

        cp -L /etc/resolv.conf /mnt/gentoo/etc/
* chroot(注意，不装systemd)
        
        mount -t proc proc /mnt/gentoo/proc
        mount --rbind /sys /mnt/gentoo/sys
        mount --rbind /dev /mnt/gentoo/dev
        chroot /mnt/gentoo /bin/bash
        source /etc/profile
        export PS1="(chroot) $PS1"
* 安装portage最新快照
        
        emerge-webrsync
* 更新portage关系树
    
        emerge --sync
* 我习惯用vim

        emerge -av vim
        eselect vi list
        eselect vi set 1
        eselect editor list
        eselect editor set 4
        . /etc/profile
* 查看new，这个环节比较重要，我这里没有影响  
    通过eselect news list和eselect news read 编号 查看news,以下都是news里的一些信息  
    如果/和/usr在不同分区，必须使用initramfs
    在package.use里加入下面这行，这样会自动安装32位类库，但会造成编译时间变长
    */* abi_x86_32     #暂时不加，编译太漫长



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

#参考
[Gentoo Linux amd64 Handbook: Installing Gentoo](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation)
