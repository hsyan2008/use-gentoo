# 安装基本系统

## 强烈建议对照[官方文档](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation)参考

* 从[gentoo官网](https://gentoo.org/downloads/)下载iso，我这里下载的是amd64下的Minimal Installation CD
* 如果要制作USB启动盘，可以使用[Rufus](http://rufus.akeo.ie/?locale=zh_CN)
* 光盘/U盘启动
* 网络链接

        ping -c 1 www.baidu.com
    如果能ping通，直接进行安装，否则通过ifconfig查看网卡名字  

    * 如果是有线网卡，网卡名称是enp3s0，执行
    
                ifconfig enp3s0 192.168.1.168 broadcast 192.168.1.255 netmask 255.255.255.0
                ip route add via 192.168.1.1 dev enp3s0
                echo "nameserver 114.114.114.114" > /etc/resolv.conf  
    * 如果是无线网卡，网卡名称是wlp3s0
        * 加密方式是wep
                    
                iwlist wlp3s0 scanning                                      #查看附近的ESSID
                iw dev wlp3s0 info
                iw dev wlp3s0 link
                ip link set dev wlp3s0 up
                iw dev wlp3s0 connect -w ESSID                               #没有密码
                iw dev wlp3s0 connect -w ESSID key 0:d:1234123412341234abc   #密码是16进制
                iw dev wlp3s0 connect -w ESSID key 0:some-password           #密码是ASCII格式  
                dhcpcd wlp3s0                                                #获取ip
                iwconfig                                                     #查看是否连接指定的SSID  
        * 加密方式是wpa/wpa2，这个方法也支持wep加密  
            在/etc/wpasupplicant/wpa_supplicant.conf里增加
                        
                    network {
                        ssid="ESSID"
                        psk="密码"
                        priority=1
                    }  
            执行
                        
                    wpa_supplicant -i wlp3s0 -c /etc/wpa_supplicant/wpa_supplicant.conf  #运行wpa_supplicant
                    dhcpcd wlp3s0                                                        #获取ip
                    iwconfig                                                             #查看是否连接指定的SSID
* 我这里为了安装方便，通过ssh安装
    * 启动ssh服务
    
            /etc/init.d/sshd start  
            或  
            rc-service sshd start
    * 修改root密码
    
            passwd
* 从其他电脑通过ssh连接过来
* 使用parted进行GPT分区(UEFI必须是GPT分区格式，且用parted或gdisk)
    * 交互式操作

            parted -a optimal /dev/sda      #-a 优化分区对齐
            (parted) mklabel gpt            #设置GPT分区格式
            (parted) unit mib
            (parted) mkpart primary 1 3     #固定为2M的bios启动分区，GPT和GRUB2一起使用时必须要有轧钢
            (parted) name 1 grub
            (parted) set 1 bios_grub on
            (parted) mkpart primary 3 131       #boot分区
            (parted) name 2 boot
            (parted) set 2 boot on              #当使用UEFI接口来引导系统时（取代BIOS），要将引导分区标识为EFI系统分区。当“boot”选项在这个分区被设置时，Parted可以自动完成此事。
            (parted) mkpart primary 131 643     #交换分区
            (parted) name 3 swap
            (parted) mkpart primary 643 -1      #简单起见，剩下的全部在/分区，实际中可以把home、var等分区独立出来
            (parted) name 4 rootfs
            (parted) quit
    * 非交互式操作
            yes | parted -a optimal /dev/sda mklabel gpt            #设置GPT分区格式
            parted -a optimal /dev/sda unit mib
            parted -a optimal /dev/sda mkpart primary 1 3     #固定为2M的bios启动分区，GPT和GRUB2一起使用时必须要有轧钢
            parted -a optimal /dev/sda name 1 grub
            parted -a optimal /dev/sda set 1 bios_grub on
            parted -a optimal /dev/sda mkpart primary 3 131       #boot分区
            parted -a optimal /dev/sda name 2 boot
            parted -a optimal /dev/sda set 2 boot on              #当使用UEFI接口来引导系统时（取代BIOS），要将引导分区标识为EFI系统分区。当“boot”选项在这个分区被设置时，Parted可以自动完成此事。
            parted -a optimal /dev/sda mkpart primary 131 643     #交换分区
            parted -a optimal /dev/sda name 3 swap
            parted -a optimal -- /dev/sda mkpart primary 643 -1      #简单起见，剩下的全部在/分区，实际中可以把home、var等分区独立出来
            parted -a optimal /dev/sda name 4 rootfs
            parted -a optimal /dev/sda p
* 格式化分区
        
        mkfs.ext2 /dev/sda2     #UEFI是mkfs.vfat -F 32 /dev/sda2
        mkfs.ext4 /dev/sda4
        mkswap /dev/sda3
        swapon /dev/sda3
* 挂载分区

        mount /dev/sda4 /mnt/gentoo
* 修改时间，注意参数要替换成具体数字(Month, Day, hour, minute and Year)

        date MMDDhhmmYYYY  
        或者(最好)  
        ntpd -q -g
* 安装stage3

        cd /mnt/gentoo
    去[gentoo官网](https://www.gentoo.org/downloads/)下载对应的stage3包

        tar xpf stage3-*.tar.* --xattrs-include='*.*' --numeric-owner
        rm -rf stage3-*.tar.*
* 配置make.conf里的编译选项
    查看CPU类型
    
        cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
    在/mnt/gentoo/etc/portage/make.conf里配置

        CFLAGS="-march=native -O2 -pipe"   #native表示使用当前系统体系结构，-pipe表示使用管道加快速度，如果内存不足，就去掉
        CXXFLAGS="${CFLAGS}"
        MAKEOPTS="-j3"          #cpu核数+1=3
* 配置源

        #按空格选择
        mirrorselect -i -o >> /mnt/gentoo/etc/portage/make.conf  #相当于再make.conf里增加GENTOO_MIRRORS="http://mirrors.163.com/gentoo/ http://gentoo.aditsu.net:8000/ rsync://ftp.iij.ad.jp/pub/linux/gentoo/ http://ftp.iij.ad.jp/pub/linux/gentoo/ http://ftp.jaist.ac.jp/pub/Linux/Gentoo/ rsync://ftp.jaist.ac.jp/pub/Linux/Gentoo/"
        mkdir -p /mnt/gentoo/etc/portage/repos.conf  
	    cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf  
* 拷贝DNS信息

        cp -L /etc/resolv.conf /mnt/gentoo/etc/
* chroot(注意，不装systemd)
        
        mount -t proc proc /mnt/gentoo/proc
        mount --rbind /sys /mnt/gentoo/sys
        mount --make-rslave /mnt/gentoo/sys  #安装systemd支持时所需要
        mount --rbind /dev /mnt/gentoo/dev
        mount --make-rslave /mnt/gentoo/dev  #安装systemd支持时所需要
        chroot /mnt/gentoo /bin/bash
        source /etc/profile
        export PS1="(chroot) $PS1"
* 挂载boot分区

        mkdir /boot
        mount /dev/sda2 /boot
* 安装portage最新快照(通常是24小时内)
        
        emerge-webrsync
* 查看new，这个环节比较重要，我这里没有影响  
    通过eselect news list和eselect news read 编号 查看news,以下都是news里的一些信息  
    如果/和/usr在不同分区，内核必须使用initramfs  
* 更新portage数据库(通常是1小时内)
    
        emerge --sync
* 选择合适的profile

        eselect profile list

        #如果是desktop
        eselect profile set 16
        或
        eselect profile set default/linux/amd64/17.0/desktop

        #如果是desktop+gnome
        eselect profile set 17
        或
        eselect profile set default/linux/amd64/17.0/desktop/gnome

        #如果是systemd
        #参考https://wiki.gentoo.org/wiki/Systemd
        eselect profile set 25
        或
        eselect profile set default/linux/amd64/17.0/systemd

        #如果是desktop+systemd
        #参考https://wiki.gentoo.org/wiki/Systemd/Installing_Gnome3_from_scratch
        eselect profile set 18
        或
        eselect profile set default/linux/amd64/17.0/desktop/gnome/systemd
* 更新@world

        emerge --ask --update --deep --newuse @world
* 我习惯用vim

        emerge -av vim
        eselect vi set vim # eselect vi set 1
        eselect vi list
        eselect editor set /usr/bin/vi # eselect editor set 3
        eselect editor list
        . /etc/profile
* 配置make.conf
    * 查看当前的USE设置
        
            emerge --info | grep ^USE
    * 查看cpu支持的指令集
    
            emerge -1v app-portage/cpuid2cpuflags
            cpuid2cpuflags        #记下执行结果，下一步用到
    * 修改/etc/portage/make.conf，显卡配置参考[Xorg/Guide](https://wiki.gentoo.org/wiki/Xorg/Guide)
    
            LINGUAS="zh_CN en"      #安装软件包的时候，如果有中文语言包，就顺便装上
            L10N="zh-CN"            #安装thunderbird(bin版本中文显示有问题)、libreoffice-l10n的时候，安装中文包
            VIDEO_CARDS="intel i965"     #请根据自己的显卡类型填入，virtualbox虚拟机里是virtualbox
            INPUT_DEVICES="libinput evdev keyboard mouse synaptics"     #synaptics是触摸板，libinput已经包含evdev
            USE="python pulseaudio git subversion gnome-keyring bash-completion vim-syntax tk icu" #icu是安装chromium需要
            CPU_FLAGS_X86="mmx mmxext sse sse2 sse3 sse4_1 ssse3"   #上一步看到的指令集
            ABI_X86="32 64"     #安装32位和64位，如果存在循环依赖，可以先注释掉
            #UEFI
            GRUB_PLATFORMS="efi-32 efi-64 pc"

* 设置时区
    
        echo "Asia/Shanghai" > /etc/timezone
        emerge --config sys-libs/timezone-data  #时间可能不对，查看下是否需要设置
* 设置字符集  
    * 在/etc/locale.gen里增加

            en_US ISO-8859-1
            en_US.UTF-8 UTF-8
            zh_CN.UTF-8 UTF-8
            zh_CN.GB2312 GB2312 
            zh_CN.GBK GBK
            zh_CN GB18030
    * 更新
        
            locale-gen
            eselect locale list
            eselect locale set zh_CN.utf8 # eselect locale set 10
            . /etc/profile
    * 继续  
        在/etc/env.d/02locale里增加

            LC_COLLATE="C"
	        env-update && source /etc/profile && export PS1="(chroot) $PS1"
* 分区挂载设置(使用genkernel需要先设置好boot挂载)  
    编辑/etc/fstab，注意，如果是UEFI，则boot分区挂载为vfat(非ext2)
    
        /dev/sda2     /boot        ext2     defaults,noatime     0 2
        /dev/sda3     none           swap      sw                0 0
        /dev/sda4     /            ext4     noatime              0 1
        /dev/cdrom    /mnt/cdrom   auto     noauto,user          0 0
* 安装内核和常用工具，注意，systemd下是genkernel-next

        emerge -av gentoo-sources pciutils genkernel mcelog app-portage/eix #iproute2已安装
        cd /usr/src/linux
        #如果使用systemd
        ln -sf /proc/self/mounts /etc/mtab
    配置参考[Configuring the Linux kernel](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Kernel)  
    配置参考[Gentoo_Kernel_Configuration_Guide](https://wiki.gentoo.org/wiki/Kernel/Gentoo_Kernel_Configuration_Guide)  
    显卡配置参考[Xorg/Guide](https://wiki.gentoo.org/wiki/Xorg/Guide)  
    无线网卡参考[Wifi](https://wiki.gentoo.org/wiki/Wifi)  
    ipv6配置参考[IPv6_router_guide](https://wiki.gentoo.org/wiki/IPv6_router_guide)  
    systemd配置参考[systemd](https://wiki.gentoo.org/wiki/Systemd)和[systemd/Installing Gnome3 from scratch](https://wiki.gentoo.org/wiki/Systemd/Installing_Gnome3_from_scratch)
    照参考进行配置，CPU类型需要按照实际选择  
    设置CONFIG_SND_HDA_PREALLOC_SIZE为2048，HD-Audio Driver的配置
    设置CONFIG_PPP_BSDCOMP，ppp用到
    
        emerge -av sys-kernel/linux-firmware   #这个是无线网卡的驱动,参考https://wiki.gentoo.org/wiki/Wifi，一般已在上一步自动安装了
        #如果需要支持lvm或raid，增加参数--lvm --mdadm
        genkernel --menuconfig all     #如果不设置，可以直接genkernel all
* 网络设置，enp3s0是网卡名字，请替换为自己的网卡名字
    * 编辑/etc/conf.d/hostname
    
            hostname="home"
    * 编辑/etc/hosts，增加主机名到127.0.0.1后面
    * 安装netifrc

            emerge --noreplace netifrc
    * 设置网络，编辑/etc/conf.d/net，修改如下
        前三行是固定ip，第四行是动态ip，选择一个就可以了

            config_enp3s0="192.168.1.173 netmask 255.255.255.0 brd 192.168.1.255"
            routes_enp3s0="default via 192.168.1.1"
            dns_servers="114.114.114.114 223.5.5.5 192.168.1.1"
            config_enp3s0="dhcp"
        如果是多ip，第一行改成

            config_enp3s0="192.168.1.173 netmask 255.255.255.0 brd 192.168.1.255  
            192.168.1.174 netmask 255.255.255.0 brd 192.168.1.255  
            192.168.1.175 netmask 255.255.255.0 brd 192.168.1.255"
    * 设置自启动
    
            cd /etc/init.d
            ln -s net.lo net.enp3s0
            rc-update add net.enp3s0 default
* 设置新系统的root密码，一定要做，否则重启后无法登陆

        passwd
* 安装一些必要的系统工具

        emerge -av syslog-ng cronie mlocate dhcpcd logrotate sudo
        #如果是pppoe
        emerge -av ppp
        #如果是无线网络
        emerge -av wpa_supplicant
        rc-update add syslog-ng default
        rc-update add cronie default
        rc-update add sshd default
        rc-update add dhcpcd default
    这里普通用户用crontab -l报'/var/spool/cron/crontabs' is not a directory, bailing out.
        
        chmod o+rx /var/spool/cron
* 安装引导，os-prober是多系统的时候有用

        emerge -av sys-boot/grub os-prober
    grub的默认配置在/etc/default/grub，比如可以把超时时间改成3秒  
    /dev/sda是启动分区所在
    
        #如果是BIOS
        grub-install /dev/sda

	    #如果是UEFI
	    grub-install --target=x86_64-efi --efi-directory=/boot
        #如果报错：Could not prepare Boot variable: Read-only file system，先执行下面命令再执行install
        mount -o remount,rw /sys/firmware/efi/efivars
        #如果重启没有出现grub界面，参考https://www.linuxidc.com/Linux/2017-10/147754.htm
        #注意引导命令是\EFI\gentoo\grubx64.efi
        #进入后执行grub-install --target=x86_64-efi --efi-directory=/boot --removable会自动生成
        #/boot/EFI/BOOT/BOOTX64.EFI文件，然后就可以重启成功了

        grub-mkconfig -o /boot/grub/grub.cfg
* 安装完成，重启，建议在shutdown后才把光盘取出，否则可能会报io错误
        
        exit
        cd
        umount -l /mnt/gentoo/dev{/shm,/pts,}
        umount -R /mnt/gentoo{/boot,/proc,}
        shutdown -h now

# 参考  
[Installing Gentoo](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation)
