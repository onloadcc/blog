+++
title = "Aria2c下载工具"
description = "Aria2c是一个支持Http,fpt和magent下载的多平台工具。介绍Aria2c在mac和Windows上使用"
date = "2018-01-30"
categories = ["Tools"]
tags = ["Aria2c","download","Tools"]
thumbnail = ""
+++

Aria2c是一个支持Http,fpt和magent下载的多平台工具。  
介绍Aria2c在mac和Windows上使用。

<https://aria2.github.io/> 

 <!--more--> 

### Mac使用
[Aria2GUI](https://github.com/yangshun1029/aria2gui/releases)直接下载即可使用。
[Persepolis Download Manager--全平台界面](https://github.com/persepolisdm/persepolis/releases)  

### Windows下使用

环境：`win10, aria2c 1.33.0`

1.到官网上下载软件后解压至目录，例`D:\download\aria2`

2.在解压的目录创建4个文件。

```
aria2.conf
aria2.log
aria2.session
HideRun.vbs
```

3.编辑`aria2.conf`，添加配置内容  
配置文件内容来自mac版`Aria2GUI`配置文件。

```
## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##
## 添加了@和默认启用的选项都是系统需要调用的，请不要随意改动否则可能无法正常运行

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
# 此项 OS X 无法使用 $HOME 及 ~/ 设置路径  建议使用 /users/用户名/downloads
dir=G:\download\
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
file-allocation=falloc
# 断点续传
continue=true

## 进度保存相关 ##

log=E:\aria2\aria2.log
log-level=warn
# 从会话文件中读取下载任务
input-file=E:\aria2\aria2.session
# 在Aria2退出时保存`错误/未完成`的下载任务到会话文件
save-session=E:\aria2\aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
save-session-interval=30

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5. 百度下载windows下测试2合适
max-concurrent-downloads=2
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=16
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
split=15
# 整体下载速度限制, 运行时可修改, 默认:0
max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0
max-overall-upload-limit=0
# 单个任务上传速度限制, 默认:0
max-upload-limit=0
# 禁用IPv6, 默认:false
disable-ipv6=false
#运行覆盖已存在文件
allow-overwrite=true
#自动重命名
auto-file-renaming=true



## RPC相关设置 ##

# 启用RPC, 默认:false
enable-rpc=true
# 允许所有来源, 默认:false
rpc-allow-origin-all=true
# 允许非外部访问, 默认:false
rpc-listen-all=true
# 事件轮询方式, 取值:[epoll, kqueue, port, poll, select], 不同系统默认值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修改, 默认:6800
# 使用本客户端请勿修改此项
#rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 取代 --rpc-user 和 --rpc-passwd 选项
#rpc-secret=token


## BT/PT下载相关 ##

# 当下载的是一个种子(以.torrent结尾)时, 自动开始BT任务, 默认:true
#follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 默认:6881-6999
listen-port=51413
# 单个种子最大连接数, 默认:55
#bt-max-peers=55
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=false
# 打开IPv6 DHT功能, PT需要禁用
#enable-dht6=false
# DHT网络监听端口, 默认:6881-6999
#dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 默认:false
#bt-enable-lpd=false
# 种子交换, PT需要禁用, 默认:true
#enable-peer-exchange=false
# 每个种子限速, 对少种的PT很有用, 默认:50K
#bt-request-peer-speed-limit=50K
# 客户端伪装, PT需要
peer-id-prefix=-TR2770-
user-agent=Transmission/2.77
# 当种子的分享率达到这个数时, 自动停止做种, 0为一直做种, 默认:1.0
seed-ratio=0
# 强制保存会话, 即使任务已经完成, 默认:false
# 较新的版本开启后会在任务完成后依然保留.aria2文件
#force-save=false
# BT校验相关, 默认:true
#bt-hash-check-seed=true
# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true
# 保存磁力链接元数据为种子文件(.torrent文件), 默认:false
bt-save-metadata=true

# bt-tracker数据来自https://github.com/ngosang/trackerslist/blob/master/trackers_all_udp.txt

bt-tracker=udp://tracker.coppersurfer.tk:6969/announce,udp://tracker.leechers-paradise.org:6969/announce,udp://tracker.opentrackr.org:1337/announce,udp://tracker.zer0day.to:1337/announce,udp://9.rarbg.com:2710/announce,udp://p4p.arenabg.com:1337/announce,udp://explodie.org:6969/announce,udp://tracker.mg64.net:6969/announce,udp://mgtracker.org:2710/announce,udp://tracker.yoshi210.com:6969/announce,udp://tracker.tiny-vps.com:6969/announce,udp://tracker.skyts.net:6969/announce,udp://tracker.tordb.ml:6881/announce,udp://tracker.piratepublic.com:1337/announce,udp://tracker.grepler.com:6969/announce,udp://tracker.filetracker.pl:8089/announce,udp://tracker.edoardocolombo.eu:6969/announce,udp://ipv4.tracker.harry.lu:80/announce,udp://91.218.230.81:6969/announce,udp://tracker.vanitycore.co:6969/announce,udp://tracker.kuroy.me:5944/announce,udp://zephir.monocul.us:6969/announce,udp://tracker.desu.sh:6969/announce,udp://bt.xxx-tracker.com:2710/announce

```
4.配置开机启动。编辑`HideRun.vbs`
```
WScript.CreateObject("WScript.Shell").Run "D:\download\aria2\aria2c.exe --conf-path=D:\download\aria2\aria2.conf",0
```
> windows下运行vbs文件:`cscript HideRun.vbs`,测试文件是否正常。如果提示错误，把符号重新输入一遍。   

5.将`HideRun.vbs`创建快捷方式到开机启动文件夹。例:`C:\Users\Admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`  

6.chrome添加[YAAW下载管理器-web界面](https://chrome.google.com/webstore/detail/yaaw/hbjpfaalboebibgfmedmjijhbjapcnki?utm_source=chrome-ntp-icon) 页面，直接使用链接下载。  

7.YAAW的chrome版本,支持右键添加到YAAW进行下载[YAAW for Chrome](https://chrome.google.com/webstore/detail/yaaw-for-chrome/dennnbdlpgjgbcjfgaohdahloollfgoc)  

8.[百度网盘插件 导出到aria2c下载功能](https://github.com/acgotaku/BaiduExporter)  

## 常见问题

### Internal server error
手动访问`http://hostname:port/jsonrpc?jsoncallback=1`，看否有返回。


#### 文档参考
[aria2配置示例](https://binux.blog/2012/12/aria2-examples/)  
[aria2配置选项文档](https://aria2.github.io/manual/en/html/aria2c.html)  
[Raspberry Pi技术笔记之四：使用aria2打造下载利器](https://pagebrin.com/2014/04/raspberry-pi%E6%8A%80%E6%9C%AF%E7%AC%94%E8%AE%B0%E4%B9%8B%E5%9B%9B%EF%BC%9A%E4%BD%BF%E7%94%A8aria2%E6%89%93%E9%80%A0%E4%B8%8B%E8%BD%BD%E5%88%A9%E5%99%A8/)  
[Aria2 & YAAW 使用说明--配置文件参考](http://aria2c.com/usage.html)  