# 使用wineQQ
* 安装wine，需要32库支持
    
        emerge -av wine
* 安装winetricks-zh，会自动安装上winetricks
    
        emerge -av winetricks-zh
* 安装字体，不然汉字显示为方块（请先看后面）

        export WINEPREFIX=~/.wine-zh
        winetricks-zh allfonts  
* 安装qqlight
    
        export WINEPREFIX=~/.wine-zh
        winetricks-zh  
    选择qqlight，然后安装  
    注意：winetricks-zh安装的软件和winetricks、wine安装的，并不在同个目录下  
    winetricks-zh：~/.local/share/wineprefixes/(winetricks-zh列表里的名字)/drive_c/  
    winetricks、wine：~/.wine/drive_c/
* 其他  
    winecfg可以看到win版本设置，我这里安装后默认的是xp  
    如果出现问题，可以修改全局设置，也可以针对具体程序进行设置
* 安装的微信无法输入，暂时采用网页版
* 重要更正，通过winetricks-zh安装的软件，安装后启动没有问题，手动启动就报组件缺失，所以重新安装
* 清理上面安装的东西，一般在.local下
* 运行winetricks，选择qq
    
        export WINEPREFIX=~/.wine
        winetricks allfonts  
        winetricks
* 运行qq 
    
    wine "~/.wine/drive_c/Program Files/Tencent/QQ/Bin/QQ.exe"  
    改成如下，上面的方式在ps里显示路径比较怪异，猜测上面也是这个问题  
        export WINEPREFIX=~/.wine
        cd "~/.wine/drive_c/Program Files/Tencent/QQ/Bin/"
        wine QQ.exe
