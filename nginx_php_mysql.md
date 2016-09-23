# 安装nginx+php
* 配置  
    在/etc/portage/make.conf里增加

        PHP_TARGETS="php5-6"
    在/etc/portage/package.use里增加

        dev-lang/php  curl intl fpm gd mysql mysqli pdo postgres pcntl sockets sqlite xmlwriter
    如果nginx默认不装fastcgi，也要加上

        www-servers/nginx NGINX_MODULES_HTTP: fastcgi
    或者在make.conf里增加

        NGINX_MODULES_HTTP="fastcgi"
* 安装

        emerge -av nginx php pecl-memcached pecl-redis pear
* 配置php
    * nginx的配置  
    在/etc/nginx/nginx.conf里的http段里增加

            autoindex on;//自动显示目录
            autoindex_exact_size off;//人性化方式显示文件大小否则以byte显示
            autoindex_localtime on;//按服务器时间显示，否则以gmt时间显示
    * 关于php的配置  
    参考[PHP](https://wiki.gentoo.org/wiki/PHP)
    在/etc/nginx/nginx.conf的server里的root后面增加

                location ~ .php$ {
                        fastcgi_pass 127.0.0.1:9000;
                        include fastcgi.conf;
                        include uwsgi_params;   #pathinfo需要
                }
    或者参考[Nginx](https://wiki.gentoo.org/wiki/Nginx)
* 启动

        /etc/init.d/nginx restart
        /etc/init.d/php-fpm restart
* 安装mysql

        emerge -av mysql mysql-workbench phpmyadmin
* 配置
        
        emerge --config dev-db/mysql
        mysql_secure_installation
        rc-update add mysql default
        rc-service mysql restart
        #下面的命令，-s选项指定web服务器，也可以在/etc/vhosts/webapp-config里指定
        /usr/sbin/webapp-config -s nginx -h localhost -u root -d /phpmyadmin -I phpmyadmin 4.5.5.1 
        #phpmyadmin的配置请参考phpmyadmin官网

