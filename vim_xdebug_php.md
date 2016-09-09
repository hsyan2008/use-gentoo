# vim+xdebug调试php
* 安装
    
        emerge -av xdebug  //这是扩展，自动安装xdebug-client了

* 配置扩展
    打开php的扩展配置xdebug.ini，修改如下配置

        xdebug.profiler_enable="1"
        xdebug.remote_enable="1"
        xdebug.remote_port="9010"
        xdebug.remote_autostart="1"
    然后重启php-fpm

        /etc/init.d/php-fpm restart
* 配置vim
    在.vimrc的插件部分加上

        Plugin 'joonty/vdebug'
    在.vimrc最下方加上

        "xdebug
        let g:vdebug_options= {
                    \    "port" : 9010,
                    \    "server" : '',
                    \    "timeout" : 20,
                    \    "on_close" : 'detach',
                    \    "break_on_open" : 1,
                    \    "ide_key" : '',
                    \    "path_maps" : {},
                    \    "debug_window_level" : 0,
                    \    "debug_file_level" : 0,
                    \    "debug_file" : "",
                    \    "watch_window_style" : 'expanded',
                    \    "marker_default" : '⬦',
                    \    "marker_closed_tree" : '▸',
                    \    "marker_open_tree" : '▾'
                    \}
* 使用
    在web目录下增加文件test.php，添加一些php代码，选择任意行，按F10是开关断点  
    按F5是开始调试，这是要尽快打开浏览器并访问http://localhost/test.php，然后回到vim  
    按F2进行步进，不需要在URL后面添加?XDEBUG_SESSION_START=1，F6是关闭
