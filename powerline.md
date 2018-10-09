# 使用powerline做状态栏
* 安装pip
    
        easy_install pip
* 升级pip
    
        pip install --upgrade pip
* 安装powerline

        pip install git+git://github.com/Lokaltog/powerline
* 安装powerline字体

        #方法一
        emerge -av media-fonts/powerline-symbols
        #方法二
        git clone https://github.com/powerline/fonts
        cd fonts;./install.sh
        cp fontconfig/50-enable-terminess-powerline.conf ~/.config/fontconfig/conf.d/
        mkdir -p ~/.config/fontconfig/conf.d
        fc-cache -vf
* 如果要设置bash，在~/.bashrc增加
    
        if [ -f `which powerline-daemon` ]; then
          powerline-daemon -q
          POWERLINE_BASH_CONTINUATION=1
          POWERLINE_BASH_SELECT=1
          . /usr/lib64/python2.7/site-packages/powerline/bindings/bash/powerline.sh
        fi
* 如果要设置tmux，在~/.tmux.conf里增加
    
        source "/usr/lib64/python2.7/site-packages/powerline/bindings/tmux/powerline.conf"
