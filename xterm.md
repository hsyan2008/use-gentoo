# xterm中文设置
* 编辑~/.Xresources，加入

		! Xft settings ---------------------------------------------------------------
		Xft.dpi: 96
		xpdf.title: PDF
		Xft.antialias: true
		Xft.rgba: rgb
		Xft.hinting: true
		Xft.hintstyle: hintslight
		
		! xterm ----------------------------------------------------------------------
		xterm*scrollBar: true
		xterm*rightScrollBar: true

        xterm*background: black
        xterm*foreground: white
		
		! English font
		xterm*faceName: DejaVu Sans Mono:antialias=True:pixelsize=16
		! Chinese font
		xterm*faceNameDoublesize: WenQuanYi Micro Hei:pixelsize=14
		
		XTerm*locale: zh_CN.UTF-8
* 执行

        xrdb -load .Xresources
* 写入到~/.xinitrc
        
        [[ -f ~/.Xresources ]] && xrdb -merge -I$HOME ~/.Xresources
* 重启xterm
