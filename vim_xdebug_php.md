# vim+xdebug调试php
* 安装
    
        emerge -av xdebug  //这是扩展，自动安装xdebug-client了

* 配置扩展
    打开php的扩展配置xdebug.ini，修改如下配置

        ;开启自动跟踪。自动打开"监测函数调用过程"的功模。
        ;该功能可以在你指定的目录中将函数调用的监测信息以文件的形式输出
        xdebug.auto_trace="1"
        xdebug.trace_output_dir="/tmp"
        xdebug.trace_output_name="trace.%c"
        xdebug.trace_format="0"
        xdebug.trace_options="0"
        xdebug.collect_includes="1"
        ;收集参数
        xdebug.collect_params="1"
        xdebug.collect_return="1"
        ;收集变量
        xdebug.collect_vars="1"
        ;显示默认的错误信息
        xdebug.default_enable="1"
        xdebug.extended_info="1"
        xdebug.manual_url="http://www.php.net"
        ;如果设得太小,函数中有递归调用自身次数太多时会报超过最大嵌套数错
        xdebug.max_nesting_level="256"
        xdebug.show_error_trace="1"
        ;开启异常跟踪
        xdebug.show_exception_trace="1"
        ;显示局部变量
        xdebug.show_local_vars="1"
        xdebug.show_mem_delta="0"
        xdebug.dump.COOKIE="NULL"
        xdebug.dump.ENV="NULL"
        xdebug.dump.FILES="NULL"
        xdebug.dump.GET="NULL"
        xdebug.dump.POST="NULL"
        xdebug.dump.REQUEST="NULL"
        xdebug.dump.SERVER="NULL"
        xdebug.dump.SESSION="NULL"
        xdebug.dump_globals="1"
        xdebug.dump_once="1"
        xdebug.dump_undefined="0"
        xdebug.overload_var_dump="2"
        ;开启把执行情况的分析文件写入到指定目录中的功能
        xdebug.profiler_enable="1"
        xdebug.profiler_output_dir="/tmp"
        xdebug.profiler_output_name="cachegrind.out.%p"
        xdebug.profiler_enable_trigger="0"
        xdebug.profiler_append="0"
        xdebug.profiler_aggregate="0"
        ;允许远程IDE调试
        xdebug.remote_enable="1"
        ;用于zend studio远程调试的应用层通信协议
        xdebug.remote_handler="dbgp"
        xdebug.remote_host="localhost"
        xdebug.remote_mode="req"
        xdebug.remote_port="9010"
        ;开启远程调试自动启动
        xdebug.remote_autostart="1"
        xdebug.remote_log=""
        xdebug.idekey=""
        xdebug.var_display_max_data="512"
        xdebug.var_display_max_depth="2"
        xdebug.var_display_max_children="128"

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
