# 快捷键工具xbindkeys
* 安装

        emerge -av xbindkeys
* 初次执行
    
        xbindkeys --defaults > ~/.xbindkeysrc
* 自动启动
    在.xinitrc里添加
        
        xbindkeys &
* 配置
    * 通过执行下面命令，可以知道按键的名字
        
            xbindkeys -k
    * 写入.xbindkeysrc文件里，第一行是双引号，内容是命令名
        第二行是快捷键，然后重启xbindkeys
    * 执行如下命令检测，scrot就是通过这个方式检测的
        
            xbindkeys -n
