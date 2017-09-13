# dns缓存
* 安装
    
        emerge -av dnsmasq
* 配置
    * /etc/conf.d/net增加下面一行，这样会自动修改/etc/resolv.conf，否则dnsmasq不起作用
    
            dns_servers="127.0.0.1"
    * 增加额外的resolv.conf，给dnsmasq用，以下两步必须操作，否则因为上一步，导致dnsmasq无法解析dns
            
            echo "nameserver 8.8.8.8" >> /etc/dnsmasq.conf.resolv
    * 修改/etc/dnsmasq.conf，找到resolv-file，修改为如下
            
            resolv-file=/etc/dnsmasq.conf.resolv
    * 启动dnsmasq
            
            rc-update add dnsmasq default
            /etc/init.d/dnsmasq restart
* 按照以下任意一种方式操作，否则每次重启网卡，/etc/resolv.conf会被修改
    * 停止dhcp，因为依赖问题，会导致很多服务停止，可能需要卸载
    * 编辑/etc/dhcpcd.conf，增加下面一行

            nohook resolv.conf
    * 编辑/etc/conf.d/net，增加下面一行(貌似静态ip方式无效)
            
            dhcpcd_enp0s31f6="--nohook resolv.conf"
* 说明  
    /etc/resolv.conf被修改为127.0.0.1后，所有dns解析都走本地的53端口，而53端口已经被dnsmasq接管  
    因为设置了resolv-file，所以dnsmasq的解析服务器是resolv-file文件里指定
* 补充
    可以考虑搭配pdnsd，参考[pdnsd + dnsmasq 解决 DNS 污染问题和国内网站域名解析问题](http://bbs.archlinuxcn.org/viewtopic.php?pid=29232)和[用pdnsd实现DNS解析加速](https://wordpress.youran.me/pdnsd/)
