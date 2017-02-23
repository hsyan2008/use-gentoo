# 使用wineQQ
* 安装wine，需要32库支持
    
    emerge -av wine
* 安装winetricks-zh，会自动安装上winetricks
    
    emerge -av winetricks-zh
* 安装字体，不然汉字显示为方块

    winetricks-zh allfonts  
* 安装qqlight
    
    winetricks-zh  
    选择qqlight，然后安装
    注意：winetricks-zh安装的软件和winetricks、wine安装的，并不在同个目录下
    winetricks-zh：~/.local/share/wineprefixes/(winetricks-zh列表里的名字)/drive_c/
    winetricks、wine：~/.wine/drive_c/
* 其他  
    winecfg可以看到win版本设置，我这里安装后默认的是xp，
    如果出现问题，可以修改全局设置，也可以针对具体程序进行设置
* 安装的微信无法输入，暂时采用网页版
