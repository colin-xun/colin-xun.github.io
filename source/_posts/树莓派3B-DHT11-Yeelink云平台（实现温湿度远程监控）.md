---
title: 树莓派3B-DHT11-Yeelink云平台（实现温湿度远程监控）
date: 2015-07-09 10:37:46
tags: [树莓派,温湿度传感器,毕业设计]
category: [嵌入式]
---
因为毕业设计选了个让自己一脸懵逼的题目，听同学说树莓派实现比较简单，所以我就花了4百大洋买了一个3B的板子和一个树莓派原装的摄像头（一百五左右，最后发现网上资料太少了!😭，而且只是搞下毕业设计，就不打算深究了，网上找个例子实现功能就可以了，不过拍的确实清晰）。好了，下面就说下怎么实现云平台实时监控温湿度把。其中有投机取巧的部分。其实我说的所有都只是一种方法，大家可以去尝试别的，下面就不赘述了，直接写过程。

- 装系统

装系统的话，直接去官网下载就行，毕竟用的树莓派，我就下了raspbian，带桌面（毕竟小白）这是下载链接https://downloads.raspberrypi.org/raspbian_latest

同时需要一个写入工具，我当时应该不是在官网下的，这是链接，速度挺快，下载完解压就行http://124.202.164.7/files/4125000005975BAB/vzidc.newhua.com/down/Win32DiskImager-0.9.5-binary.zip

然后需要一张4g以上的sd卡，反正我用的8g，我用的就是很早以前手机里的内存卡，同时需要一个读卡器，打开读写工具，然后把解压后的img系统文件写入到内存卡，就可以了

- 登录树莓派

第一次登录用ssh工具，首先你需要知道局域网给你的树莓派分配的IP地址，可以使用一个端口扫描工具，看看哪儿22端口是开放的，然后xshell链接

- 显示图形界面

我也不先配置别的了，先让大家看看图形界面吧，上vncviewer下载客户端，然后在xshell输入sudo apt-get install tightvncserver然后等读码完成，然后输入vncserver，接下来它让你设置密码，我设置12345678，然后确认再输入一次，接着打开vncviewer，在地址栏输入树莓派ip:1回车，然后输入12345678，回车就可以看到图形界面了

- 树莓派连接上无线网路

因为树莓派没有自带vim，所以我就先安装了vim，命令式sudo apt-get install vim

然后sudo vim /etc/network/interfaces 然后修改成下列样式

    auto lo
    iface loinet loopback
    ifaceeth0 inet dhcp
    autowlan0
    allow-hotplugwlan0
    ifacewlan0 inet dhcp
          wpa-ssid 要连入的WiFi名
          wpa-psk WiFi密码

然后sudo /etc/init.d/networking restart 但是这时候会分配一个新的ip，然后xshell创建一个新的连接就行了

修改配置

这是树莓派3B的界面，别的修改项含义可以参考

http://blog.csdn.net/xdw1985829/article/details/38816375

，我修改了第一项，让文件系统覆盖整个sd卡，同时修改了时区，如果不修改时区的话，接下来上传的数据时间不正确

安装WiringPi

sudo apt-get install wiringPi 安装完可以测试一下gpio readall然后会显示一些东西就证明可以了

接下来就是程序和怎么连接到yeelink上了

下载我整理好的程序，test.c是源文件可以使用gcc编译一下，编译命令式gcc –Wall –o 输出的文件名  编译的文件名 –lwiringPi 然后生成一个你要生成的文件名

接下来修改shell脚本,Yeelink教程网上很多，我就不介绍了，得到两个传感器的URL就行，还有你的API key，替换成你的API key和URL，同时还有你编译.c生成的文件的路径

这是我写的，有点投机取巧✌️，勉强能用。

链接：http://pan.baidu.com/s/1nuXLmAl 密码：yjvq
