# 安装使用gitbook
* 安装nodejs

        emerge -av nodejs
* 安装gitbook-cli（使用方法见[GitbookIO/gitbook-cli](https://github.com/GitbookIO/gitbook-cli)）
        
        npm install gitbook-cli -g
* 查看本地gitbook版本

        gitbook ls
        gitbook versions
* 查看服务器所有gitbook版本

        gitbook ls-remote
* 安装最新版  
    说明：npm里还有个gitbook，运行的时候会提示安装gitbook-cli，这两者都会创建gitbook命令，所以会相互覆盖，而使用本文这种方式安装，通过gitbook-cli管理gitbook了

        gitbook versions:install latest
* 创建git项目
    
        mkdir gitbook;cd gitbook;
* 创建必要文件
        
        touch README.md SUMMARY.md
* 编辑SUMMARY.md，注意，必须是列表的格式，否则用gitbook init无法初始化目录

        * [安装基本系统](install.md)
        * [安装图形](x.md)
* 生成文件

        gitbook init
* 生成html
    
        gitbook build
* 有些md目录下面有个book.json，里面有插件依赖，可以通过下面命令安装插件
    
        gitbook install
* 升级gitbook

        gitbook update
