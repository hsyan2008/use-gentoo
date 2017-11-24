# 使用tmpfs加快编译
* 修改fstab，挂载/var/tmp/portage目录，因为很多软件比较大，所以设置大小为4G  
    
        tmpfs		/var/tmp/portage	tmpfs	size=4G,uid=portage,gid=portage,mode=775,noatime	0 0
    如果/var/tmp已经挂载，则fstab应该如下

        tmpfs /var/tmp         tmpfs rw,nosuid,noatime,nodev,size=4G,mode=1777 0 0
        tmpfs /var/tmp/portage tmpfs rw,nosuid,noatime,nodev,size=4G,mode=775,uid=portage,gid=portage,x-mount.mkdir=775 0 0
* 执行命令

        mount /var/tmp/portage
* 如果有些软件实在太大了，4G无法满足，则再做如下配置
        * 编辑/etc/portage/env/notmpfs.conf，增加如下内容

                PORTAGE_TMPDIR="/var/tmp/notmpfs"
        * 执行命令

                mkdir /var/tmp/notmpfs
                chown portage:portage /var/tmp/notmpfs
                chmod 775 /var/tmp/notmpfs
        * 编辑/etc/portage/package.env，增加

                app-office/libreoffice notmpfs.conf
                mail-client/thunderbird notmpfs.conf
                sys-devel/gcc notmpfs.conf
                www-client/chromium notmpfs.conf
                www-client/firefox notmpfs.conf
* 如果想调整大小，则执行

        mount -o remount,size=N /var/tmp/portage
* 重点：tmpfs里的内容，编译完毕会自动消失，或者重启后就消失