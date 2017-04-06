# shell设置vi模式
* 设置当前终端
    
        set -o vi
* 设置当前用户，把上述命令加入~/.bashrc
* 设置所有用户，把上述命令加入/etc/bash/bashrc
* 设置为vi模式后，Ctrl+l无法清屏，可以在~/.inputrc或者/etc/inputrc里加入
    
        "\C-l": clear-screen 
