# 添加本地Overlay
最近为了安装genymotion案桌模拟器，在官方overlay里找到necromancy-overlay，但已经废弃，无法安装。  
辗转找到[Kelvin-Gentoo-Overlay](https://github.com/Kelvin-Ng/Kelvin-Gentoo-Overlay)，学习了下如何手动添加overlay
* git clone到目录kelvin，这个目录名字在[repo_name](https://github.com/Kelvin-Ng/Kelvin-Gentoo-Overlay/blob/master/profiles/repo_name)
    
        cd /var/lib/layman
        git clone https://github.com/Kelvin-Ng/Kelvin-Gentoo-Overlay kelvin
* 添加配置  
    查看/etc/portage/make.conf，可以看到配置文件是/var/lib/layman/make.conf（假如配置过layman）
    把目录/var/lib/layman/kelvin添加到/var/lib/layman/make.conf里
* 安装

        emerge -av genymotion