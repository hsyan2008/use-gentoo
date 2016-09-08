# 设置默认程序
* 文件说明
    1. /usr/portage/mate-base/mate-session-manager/files/defaults.list  
        清单，并没有起作用，可以在设置的时候做参考
    2. /home/saxon/.config/mimeapps.list  
        有Default Applications和Added Associations  
        Default Applications的优先级高于第三点  
        Added Associations并没有效果  
    3. /home/saxon/.local/share/applications/mimeapps.list  
        只有Default Applications，优先级低于第二点  
* 查看方式
        
        xdg-mime query default x-scheme-handler/https
* 设置方式
        
        xdg-mime default chromium-browser-chromium.desktop x-scheme-handler/https
        
    设置的结果在.local目录下，如果.config下已有设置，则不生效，手动处理
