# 使用privoxy
* 说明  
    这个工具是用来转换socket5为http和https代理
* 安装

        emerge -av net-proxy/privoxy
* 设置
    打开/etc/privoxy/config，修改成如下

        listen-address  0.0.0.0:8118
        forward-socks5 / 0.0.0.0:7070 .
* 启动

        /etc/init.d/privoxy restart
    通过netstat -nlutp 可以看到privoxy监听8118端口，监听的地址是0.0.0.0
* 浏览器设置代理到0.0.0.1:8118
* 命令行下可以（参考[Linux设置代理上网](http://blog.163.com/likaifeng@126/blog/static/320973102012221111622825/)）

        export http_proxy=http://127.0.0.1:8118
        export https_proxy=https://127.0.0.1:8118
    执行下面命令进行验证

        curl  https://golang.org/
* 为了方便
    开机自启动
    
        rc-update add privoxy default
    在~/.bashrc里写入
    
        export http_proxy=http://127.0.0.1:8118
        export https_proxy=https://127.0.0.1:8118
        export no_proxy=127.0.0.1,192.168.1*
    或者

        alias proxy_privoxy="export http_proxy=http://127.0.0.1:8118;export https_proxy=http://127.0.0.1:8118;export no_proxy=127.0.0.1,192.168.10.*"
        alias proxy_off="unset http_proxy;unset https_proxy;unset no_proxy"

    这样可以
        
        proxy_privoxy   #开启privoxy代理
        proxy_off       #关闭代理
* 补充  
    如果只是临时性，建议使用proxychains

