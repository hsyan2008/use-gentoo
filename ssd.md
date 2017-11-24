# 使用ssd硬盘优化
### 参考文档  
* [关于对新装Linux的固态硬盘（SSD）做优化配置](http://tieba.baidu.com/p/2244307121)
* [SSD - Gentoo Wiki](https://wiki.gentoo.org/wiki/SSD)
***
### 步骤
* 安装系统前，确定BIOS中SATA工作在AHCI模式下，而非IDE模式
* 执行/sbin/fdisk -l /dev/sda确认是4K对齐(sda是ssd硬盘)
* 修改/etc/fstab，增加noatime，不要增加discard，这个可以通过下面的步骤处理
* root的crontab定时执行fstrim，每天2次

        25 0,12 * * *   /sbin/fstrim -v /
* 把一些写入就删除的文件，移动到其他地方，比如portage编译的相关文件，可以存放到tmpfs

