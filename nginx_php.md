# 安装nginx+php
* 配置  
    在/etc/portage/make.conf里增加
    PHP_TARGETS="php5-6"
    在/etc/portage/package.use里增加
    dev-lang/php  curl fpm gd mysql mysqli pdo postgres pcntl sockets sqlite
    如果nginx默认不装fastcgi，也要加上
    www-servers/nginx NGINX_MODULES_HTTP: fastcgi
    或者在make.conf里增加
    NGINX_MODULES_HTTP="fastcgi"
* 安装

        emerge -av nginx php
* 配置
    参考https://wiki.gentoo.org/wiki/PHP
    在/etc/nginx/nginx.conf的server里的root后面增加
                location ~ .php$ {
                        fastcgi_pass 127.0.0.1:9000;
                        include fastcgi.conf;
                }
    或者参考https://wiki.gentoo.org/wiki/Nginx
* 启动

        /etc/init.d/nginx restart
        /etc/init.d/php-fpm restart
