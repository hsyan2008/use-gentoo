# 安装微信
* 步骤如下，参考[electronic-wechat](https://github.com/geeeeeeeeek/electronic-wechat)
        
        # Clone this repository
        git clone https://github.com/geeeeeeeeek/electronic-wechat.git
        # Go into the repository
        cd electronic-wechat
        # Install dependencies and run the app
        npm install && npm start
* 启动的时候报错no such file or directory, open 'trayConfig.json'
    
        mkdir ~/.config/electronic-wechat/
        mkdir ~/.config/electronic-wechat/trayConfig.json
        echo '{"color":"white"}' > ~/.config/electronic-wechat/trayConfig.json
