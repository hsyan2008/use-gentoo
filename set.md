# 基本配置和通用软件安装

* 如果需要，设置允许ssh远程登陆root，编辑/etc/ssh/sshd_config,修改成
    
        PermitRootLogin yes
    重启  

        /etc/init.d/sshd restart
* 清理安装文件，创建用户，$USER请替换成自己的用户名

        useradd -m -G users,wheel,audio,lp,video,usb,cdrom,portage -s /bin/bash $USER
        rm /stage3-*.tar.bz2*
* 配置bash
    拷贝默认配置

      cp /etc/skel/.bash* ~/
      cp -r /etc/skel/.ssh ~/
    编辑~/.bashrc,增加

        alias ll='ls --color=auto -l'
        alias rm='rm -i'
        HISTCONTROL=ignoredups      #连续名字不重复出现，和下一条冲突，选择一个即可
        HISTCONTROL=erasedups       #所有的重复只出现一次，和上一条冲突，选择一个即可
        HISTSIZE=10000
        HISTFILESIZE=10000
* 更新的源配置，如果安装的时候已操作，请跳过  
    不在make.conf了，在/etc/portage/repos.conf/，见[Project:Portage/Sync](https://wiki.gentoo.org/wiki/Project:Portage/Sync)

        mkdir -p /etc/portage/repos.conf
        cp /usr/share/portage/config/repos.conf /etc/portage/repos.conf/gentoo.conf

* 设置编译缓存

        emerge -av dev-util/ccache
    在make.conf里增加,增加后

        FEATURES="ccache"
        CCACHE_SIZE="2G"     #设置大点
        CCACHE_DIR="/var/tmp/ccache"
    在root用户和普通用户的~/.bash_profile里增加

        PATH="/usr/lib/ccache/bin:/opt/bin:${PATH}"
    ccache的常用命令如下

        ccache -s       #查看状态
        ccache -C       #清理缓存

* 安装x(如果想用图形界面)
    参考[Xorg/Guide](https://wiki.gentoo.org/wiki/Xorg/Guide)、[AMD/ATI显卡](https://wiki.gentoo.org/wiki/Radeon)、[Intel显卡](https://wiki.gentoo.org/wiki/Intel#Kernel)、
    [Virtualbox虚拟机](https://wiki.gentoo.org/wiki/VirtualBox#Windows_guests)

        emerge -av eix sudo
        emerge -av x11-base/xorg-drivers	//显卡驱动
        #emerge -av xorg-server      #x服务端，没有这个就没有图形界面
        emerge -av media-libs/libtxc_dxtn   #为mesa开启完整的S3TC支持

* 安装第三方源

        emerge -av app-portage/layman #安装仓库管理工具,类似管理archlinux的core、extra、aur等
	    echo "source /var/lib/layman/make.conf" >> /etc/portage/make.conf
    	layman -L     #列出所有第三方源
    	layman -a gentoo-zh #增加源，如果前面是红色星星，表示没有装git，其他也类似，可以为layman添加use
    	layman -S     #更新
    	eix-update  
    如果不让该layman不参与update，可以在/etc/portage/package.mask里加入  
        
        */*::gentoo-zh

* 浏览器

        emerge -av firefox chromium chrome-binary-plugins
    在/usr/local/bin里增加个脚本chrome，内容如下

        #/bin/bash
        chromium-browser -enable-easy-off-store-extension-install -disable-application-cache -disk-cache-dir=/dev/shm/chromium -media-cache-dir=/dev/shm/chromium -disk-cache-size=104857600 -media-cache-size=104857600 &
    以后就可以执行chrome来启动，这样可以把缓存写到内存，而不是硬盘，提高性能
* 字体

        emerge -av wqy-bitmapfont wqy-microhei wqy-unibit wqy-zenhei    #文泉驿字体，根据需要选择，一般编程用wqy-microhei
        emerge -av media-fonts/inconsolata      #推荐字体，建议设置成大小14
        eselect fontconfig list     #字体列表
        eselect fontconfig enable n     #字体设置，n想要启用的字体序号

* 安装常用软件

        emerge -av sys-apps/usb_modeswitch net-misc/bridge-utils     #自动挂载，高级网络配置(virtualbox)
        emerge -av dev-vcs/git subversion mercurial bzr      #版本管理
        emerge -av guake tilda(可能需要安装vte的特定版本)   #下拉式终端，支持透明，选择一个即可
        emerge -av terminator       #分屏式终端
        emerge -av arandr 	//分辨率和屏幕设置，xrandr在安装X的时候一般已经自动装上
        emerge -av dmenu    #快速启动器，支持模糊搜索
        emerge -av gnome-icon-theme xcursor-themes     #系统图标和鼠标主题
        emerge -av slock xautolock scrot aria2 numlockx      #分别是锁屏、截图(linux自带的import命令也可以截图)、下载、小键盘
        emerge -av adobe-flash gpicview nautilus  #分别是falsh、图片、文件浏览
        emerge -av fcitx fcitx-configtool fcitx-sunpinyin   #输入法，USE需要加上gtk2 gtk3，带剪切板管理
        emerge -av fcitx-table-extra        #五笔输入法，需要在附加组件里勾选“码表”
        emerge -av gentoo-bashcomp bash-completion      #命令行自动完成
        emerge -av pptpclient rp-pppoe      #pptp vpn pppoe
        emerge -av compton      #透明
        emerge -av app-admin/keepassx       #密码管理
        emerge -av autojump     #快速cd
        emerge -av gentoolkit #gentoo的管理脚本，如equery
        emerge -av bind-tools     #dig、nslookup、host、nsupdate等工具
        emerge -av traceroute #查看ip路由
        emerge -av netkit-telnetd     #telnet
        emerge -av file-roller rar     #压缩包管理
        emerge -av cdrtools     #命令行刻录工具
        emerge -av app-office/libreoffice
        emerge -av tmux tmux-mem-cpu-load app-text/tree expect
        emerge -av sublime-text    #注意fcitx的use必须要有gtk2 gtk3，否则无法输入
        emerge -av wireshark    #网络嗅探，安装后执行gpasswd -a $USER wireshark;newgrp wireshark
        emerge -av genlop   #统计各个软件的安装耗时，如genlop -t firefox
        emerge -av xchm   #chm文件阅读
        emerge -av apache-tools   #如果不想用apache，但想用ab的话，安装这个，也可以使用wrk来压测，wrk -d 10s url
        emerge -av veracrypt    #truecrypt新版
        emerge -av aliedit      #支付宝控件，需要安装gentoo-zh的layman
        emerge -av privoxy      #ssh隧道是socket5，可以通过这个工具转成http和https
        emerge -av proxychains  #在终端下使用socket5代理
        emerge -av phddns       #花生壳，内网机器需要做端口映射
        emerge -av filezilla    #ftp客户端
        emerge -av cmus    #终端下音乐播放器
        emerge -av aircrack-ng    #破解wep密码，教程见[破解WIFI无线网络的WEP密钥](https://linux.cn/article-2382-1.html)
        emerge -av netcat       #网络工具nc
        emerge -av xprop    #应该已经被其他软件依赖安装了，使用方法是在命令行执行命令，然后鼠标点击窗口内，就可以看到class
        emerge -av brasero  #图形化刻录软件，命令行可以用cdrecord
        emerge -av transmission     #bt下载工具
        emerge -av kodi         #xbmc，影视中心
        emerge -av ntfs3g       #ntfs格式的移动硬盘挂载是只读，安装这个才可以读写
		emerge -av app-emulation/virtualbox-extpack-oracle app-emulation/virtualbox-additions    #virtualbox虚拟机

* 设置
    * 如果是virtualbox里安装的gentoo
        
            gpasswd -a $USER vboxguest
            rc-update add /etc/init.d/virtualbox-guest-additions default
    * 按照安装信息操作
            
            rc-update add lvm boot
    * 用户组设置和权限设置

            gpasswd -a $USER plugdev     #USB
            gpasswd -a $USER uucp          #蓝牙、ppp、rfcomm

            chown root:mail /var/spool/mail/
            chmod 03775 /var/spool/mail/

            emerge --config =mail-mta/nullmailer-2.0-r1
    * 自动完成    
        为root开启自动完成

            eselect bashcomp list | grep '\[' | grep -v * | awk '{print $2}' | xargs -r -n 1 eselect bashcomp enable
        为所有用户开启自动完成

            eselect bashcomp list | grep '\[' | grep -v * | awk '{print $2}' | xargs -r -n 1 eselect bashcomp enable --global
    * tmux设置和终端提示设置
        ~/.bashrc里增加

            alias tmux='tmux -2'        #使用tmux+vim必须要这个
            PS1='\[\033[01;32m\][\u@\h\[\033[00m\] \[\033[01;34m\]\W]\[\033[00m\]\\$ '
    * 退出的时候保存history
        在~/.bash_logout增加

            history -a
            #clear
    * 透明  
        配置文件[compton](https://github.com/chjj/compton/blob/master/compton.sample.conf)，可以根据需要更改，如

            fading = false  #表示是否开启动画
            inactive-opacity = 0.8    #表示窗口失去焦点的透明度
            active-opacity  = 0.8      #表示窗口得到焦点的透明度
    * 快速cd
        在~/.bashrc里加入

            [[ -s /etc/profile.d/autojump.sh ]] && . /etc/profile.d/autojump.sh
        使用的时候，先cd几个目录，然后j 关键字即可，比如
            cd /opt/
            cd /usr/local/bin
            cd /var/log
            j bin   #就可以到之前cd过的/usr/local/bin
    * 设置~/.xintrc,参考[Display Power Management Signaling](https://wiki.archlinux.org/index.php/Display_Power_Management_Signaling_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

            #xset是图形下的设置，setterm是字符界面下的设置
            xset -dpms  #禁止自动关闭显示器，比如休眠
            xset s noblank  #关闭屏保的背景
            xset s noexpose #关闭屏保
            xset s 0    #关闭屏保，这个相当是把时间设置为无限
            xset -b     #关闭终端响铃，图形模式下
            export XIM=fcitx
            export XMODIFIERS="@im=fcitx"
            export GTK_IM_MODULE=fcitx
            export QT_IM_MODULE=fcitx
            export XIM_PROGRAM=fcitx

	        fcitx >/dev/null 2>&1 &
    	    guake >/dev/null 2>&1 &
	        compton --config ~/.compton.sample.conf >/dev/null 2>&1 &
            xautolock -time 1 -locker slock &   #1分钟没有动作就锁屏
            numlockx &
* 声音

        emerge -av alsa-utils
        rc-update add alsasound boot
        getfacl /dev/snd/controlC0 | grep $USER
        /etc/init.d/alsasound start
        alsamixer   #方向键向上是加音量，ESC是退出，m是开关，尤其是第二列，要把MM变为00
        emerge -av volumeicon       #托盘上图表
        volumeicon &        #加入到.xinitrc
* 无线网卡，参考[Wifi](https://wiki.gentoo.org/wiki/Wifi)

        emerge -av sys-kernel/linux-firmware
        emerge -av wireless-tools     
        iwconfig wlan0 txpower on     #打开无线网卡电源
        iwlist wlan0 scan          #列出周围无线网络
    iwconfig只支持wep,不支持wpa/wpa2，所以本案例没用iwconfig，iwconfig可以参考[Preparing_for_wireless_access](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation#Preparing_for_wireless_access)  
    wpa2加密采用wpa_supplicant工具，参考  
    [Wireless_tools](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Networking#Wireless_tools)  
    [Wpa_supplicant](https://wiki.gentoo.org/wiki/Wpa_supplicant)  
    [在命令行中管理 Wifi 连接](https://linux.cn/article-4015-1.html)

        emerge -av wpa_supplicant     #不要加入自动运行，因为dhcpd会自动读取配置来启动的

    编辑/etc/wpa_supplicant/wpa_supplicant.conf，输入

        # Allow users in the 'wheel' group to control wpa_supplicant
        ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=wheel
        #自动扫描无线
        ap_scan=1
        # Make this file writable for wpa_gui / wpa_cli
        update_config=1
        #多个就写多个network
        network={
                ssid="SSID"   #WIFI名字
                psk="PASS"      #WIFI密码
                scan_ssid=1
                proto=RSN
                key_mgmt=WPA-PSK
                group=CCMP TKIP
                pairwise=CCMP TKIP
                priority=5      #优先级
        }
    在/etc/conf.d/net里增加(有线的配置还留着，会出现两个网卡同时联网的情况)

        modules_wlp3s0="wpa_supplicant"
        config_wlp3s0="dhcp"

    然后可以执行下面的命令查看是否成功启动，以及log

        wpa_supplicant -Dnl80211 -iwlp3s0 -C/var/run/wpa_supplicant/ -c/etc/wpa_supplicant/wpa_supplicant.conf -dd
    然后执行

        ln -s /etc/init.d/net.lo /etc/init.d/net.wlp3s0
        /etc/init.d/net.wlp3s0 restart     #这样就算无线重启，也会自动连接
        rc-update add net.wlp3s0 default

* startx+awesome
    * 安装

            emerge -av awesome #建议最新版，稳定版和chromium的兼容性有问题
    * 设置
        * 改成终端登录自动启动startx，参考[X without Display Manager](https://wiki.gentoo.org/wiki/X_without_Display_Manager)

            在~/.bash_profile最后增加

                tty=`tty`
                #[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx      
                #archlinux里可以这样，但gentoo里无法成功，后来改成如下,注意官方文档里的写法也无法成功
                [[ -z $DISPLAY && "$tty" = "/dev/tty1" ]] && exec startx
        * 在~/.xinitrc最后增加，特别强调，任何时候，一定要保持下面这个内容在最后

                exec ck-launch-session dbus-launch --sh-syntax --exit-with-session awesome
* 更新系统

        emaint sync -a
        emerge -av --update --newuse --deep --with-bdeps=y @world

* 大功告成，重启

        shutdown -r now
