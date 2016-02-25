# 使用lantern
* 安装

        emerge -av net-proxy/lantern-bin
* 启动

        lantern -headless  //加-headless不会弹出浏览器，时灵时不灵，如果有人知道，请告诉我，谢谢
    通过netstat -nlutp 可以看到lantern监听8787和16823端口，监听的地址是127.0.0.1
* 浏览器设置代理到127.0.0.1:8787
* 命令行下可以（参考[Linux设置代理上网](http://blog.163.com/likaifeng@126/blog/static/320973102012221111622825/)）

        export http_proxy=http://127.0.0.1:8787
        export https_proxy=https://127.0.0.1:8787
    执行下面命令进行验证

        curl  https://golang.org/
* 为了方便，写入启动脚本里  
    在.xinitrc里写入
    
        lantern -headless &
    在~/.bashrc里写入
    
        export http_proxy=http://127.0.0.1:8787
        export https_proxy=https://127.0.0.1:8787