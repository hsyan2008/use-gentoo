# 显示器亮度自动调整
* 安装redshift

        emerge -av redshift
* 启动

        #-l是经纬度，可以通过地图查看，-b是白天和晚上的亮度
        redshift -l 31:121 -b 0.7:0.7
* 加入.xinitrc

        redshift -l 31:121 -b 0.7:0.7 >/dev/null 2>&1 &

