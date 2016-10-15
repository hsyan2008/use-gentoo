# ibus输入法
* 安装基本软件

        emerge -av ibus ibus-pinyin
* 安装具体的输入法
    * libpinyin(设置无法更改)

            emerge -av ibus-libpinyin
    * sunpinyin
        
            emerge -av ibus-sunpinyin
    * 其他
* 在.xinitrc里增加

        export XMODIFIERS="@im=ibus"
        export GTK_IM_MODULE="ibus"
        export QT_IM_MODULE="xim"
        #export QT_IM_MODULE=ibus
        ibus-daemon -drx &
* 重启
