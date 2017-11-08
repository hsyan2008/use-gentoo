# bash自动完成
* 安装系统自带的
    
        emerge -av bash-completion
* 安装额外的
        
        git clone https://github.com/Bash-it/bash-it
        cd bash-it/completion/available/
        mkdir -p $HOME/.local/share/bash-completion/completions
        #如果安装go
        cp go.completion.bash $HOME/.local/share/bash-completion/completions/go

