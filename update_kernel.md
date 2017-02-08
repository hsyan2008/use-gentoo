# 内核升级
可以参考[升级gentoo kernel](http://huangda-hd.blog.163.com/blog/static/81808426201441010235543/)


* 选择新内核并编译，参考[Kernel/Upgrade](https://wiki.gentoo.org/wiki/Kernel/Upgrade)
    * 切换内核
        
            eselect kernel list
            eselect kernel set 2
            cd /usr/src/linux
    * 清理并生成当前配置
        
            make distclean
            zcat /proc/config.gz > /usr/src/linux/.config
    * 编译内核
        * 说明：  
            * genkernel用的是默认配置，会导致有些设置不见了，所以升级内核的时候必须指定配置文件，默认也不是读取.config  
            * genkernel默认会删除/usr/src/linux/.config，所以必须备份配置，然后指定备份的配置为参数，这样就不会导致配置文件不见而失败  
            * genkernel使用的默认配置在/usr/share/genkernel/arch/x86_64/，完成后，会在/etc/kernels/里生成一个配置文件  
            * genkernel all生成System.map-genkernel、initramfs-genkernel和kernel-genkernel  
            * make install生成System.map、config和vmlinuz  
            * 综合以上，grub.cfg里配置会有以下几种类型，我使用的是第二个     
                * linux后是vmlinuz     （make install）
                * linux后是kernel-genkernel，initrd后是initramfs-genkernel （genkernel all）
                * linux后是vmlinuz，initrd后是initramfs-genkernel （make install+genkernel --install initramfs）     
                * 2和3共存 （genkernel all+make install）
        
        * 步骤一、

                cp .config .config.bak
                genkernel --kernel-config=.config.bak --menuconfig all
                emerge --ask @module-rebuild    //virtualbox需要，最好每次都运行下，重启后也执行
        * 步骤二、  
            参考[GRUB2](https://wiki.gentoo.org/wiki/GRUB2)
        
                grub2-mkconfig -o /boot/grub/grub.cfg //注意新版grub的命令是grub-mkconfig
        * 步骤四、  
            参考[Kernel/Removal](https://wiki.gentoo.org/wiki/Kernel/Removal)
            
                emerge --ask --depclean gentoo-sources
                eclean-kernel -n 3      //保留三个最新的内核
                rm -r /usr/src/linux-3.X.Y
                rm -r /lib/modules/3.X.Y
                rm /boot/kernel-3.X.Y
                rm /boot/System.map-3.X.Y
                rm /boot/config-3.X.Y
                grub2-mkconfig -o /boot/grub/grub.cfg
        * 步骤五、  
            如果重启后virtualbox无法启动，执行下面的命令

                emerge --ask @module-rebuild     //可以不执行
                modprobe vboxdrv     //解决提示 /etc/init.d/vboxdrv setup
                modprobe vboxnetflt     //解决提示 Failed to open/create the internal network
                modprobe vboxnetadp 
                modprobe vboxpci
