+++
title = "AWS搭建shadowsocks服务器"
description = "AWS搭建shadowsocks服务器"
date = "2018-02-02"
categories = ["Tools"]
tags = ["awz","shadowsocks","tools"]
thumbnail = ""
+++

本篇主要说下shadowsocks搭建过程中踩的坑，AWS申请和如何连接就不说了。

 <!--more-->

直接进入主题，连接到AWS之后。

### shadowsocks安装

	yum install python-setuptools && easy_install pip
	pip install shadowsocks


### 添加配置文件
	
	sudo  vim /etc/shadowsocks/config.json

添加内容

	{
	 "server":"0.0.0.0",
	 "server_port":8388,
	 "local_address":"127.0.0.1",
	 "local_port":1080,
	 "password":"yourpassword",
	 "timeout":300,
	 "method":"aes-256-cfb",
	 "fast_open":false
	}


### 启动

	sudo /usr/local/bin/ssserver -c /etc/shadowsocks/config.json -d start

#### 常用命令

    ssserver -c /etc/shadowsocks.json ##前台运行
    ssserver -c /etc/shadowsocks.json -d start  ##后台运行
    ssserver -c /etc/shadowsocks.json -d stop  ##停止后台运行
    tail -f -n 100 /var/log/shadowsocks.log  ##查看运行日志


### 添加开机启动

编辑`sudo vim /etc/rc.local`文件，最后添加`sudo /usr/local/bin/ssserver -c /etc/shadowsocks/config.json -d start`即可。

### 重点来啦

配置好以后，手机端连接成功，但测试一直上不了网。找问题呀。

- 查看日志`cat /var/log/shadowsocks.log`，客户端请求都会出现。

	参考[Troubleshooting](https://github.com/shadowsocks/shadowsocks/wiki/Troubleshooting)以为是`/etc/resolv.conf`文件问题，更改后重启文件又会还原。

- 更改`/etc/resolv.conf`文件正确姿势

	`sudo vim /etc/sysconfig/network-scripts/ifcfg-eth0`

	在最后添加

	```
	DNS1=8.8.8.8   #google dns服务器, 根据实际情况更换　　
	DNS2=8.8.4.4   #google dns服务器, 根据实际情况更换
	```

	然后重启服务器

	`/etc/resolv.conf`文件`nameserver`字段会自动改为`DNS1`和`DNS2`。

还是不行，吃了个饭，想了想。看下手机中配置，使用`shadowsocks`客户端。

<center>
	<img alt="shadowsocks_client_setting.png" src="shadowsocks_client_setting.png" width="240" height="215">
</center>

- 试着把`IPv6路由`和`DNS转发`关掉，居然可以上网了！！！又试着两个关掉一个，发现是`DNS转发`影响的不能上网。到网上搜索了下，`shadowsocks怎么设置DNS转发`，没啥结果，看到了`UDP`相关的。是不是`UDP`端口没开？把`UDP`的端口打开后，可以使用！！！


## 原因：`服务器端口设置`

- 服务器入站端口需要把`Custom TCP Rule`和`Custom UDP Rule`都开启才可以。

<center>
	<img src="shadowsocks_aws_port.png" alt="shadowsocks_aws_port.png">
</center>


### 参考

[shadowsocks-官方文档](https://github.com/shadowsocks/shadowsocks/blob/master/README.md)  
[Troubleshooting-常见问题](https://github.com/shadowsocks/shadowsocks/wiki/Troubleshooting)  
[使用亚马逊AWS搭建免费的Shadowsocks服务器](http://celerysoft.github.io/2016-01-15.html)  
[CentOS DNS Resolv 设置和重启无效的解决方法](https://www.quyu.net/info/264.html)  




