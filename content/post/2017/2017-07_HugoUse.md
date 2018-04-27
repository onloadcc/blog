+++
title = "Hugo 使用笔记"
description = "Hugo 使用过程中记录，不定期更新"
date = "2017-09-13"
categories = ["Tools"]
tags = ["hugo","go","blog"]
thumbnail = "img/hugo-logo.png"
+++


Hugo是由Go语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署。


<!--more-->

### 开始使用

#### 创建站点  

```bash
hugo new site hello 
```

#### 添加主题    

```bash
cd hello/themes

$ git clone https://github.com/vimux/mainroad
```

使用[mainroad](https://github.com/Vimux/Mainroad/)主题  

下载完成后进入`themes/mainroad/exampleSite`目录，将所有文件拷贝并覆盖到`hello`根目录。

#### 添加文章

添加到`post`目录

```bash
hugo new post/first.md
```

#### 本地运行

```bash
hugo server
```

浏览器访问 <http://localhost:1313/>

#### 生成网站目录

```bash
hugo
```

生成之后在`hello`下生成`pulic`目录，全部上传至网站即可。

### 添加robots.txt文件


1.在`config.toml`中添加`enableRobotsTXT=true`。  
2.`static`目录下添加`robots.txt`文件，并编辑不需要抓取的网址。  
3.重新生成网站即可生成`robots.txt`文件。  


### 文章编写

#### 文章列表预览内容控制

在文章内部，不需要展示在列表中进行预览的前面添加一行 `<!--more-->` ，列表预览
就只显示前面的内容，后面的内容不再显示。


### 参考  
[使用Hugo搭建惊艳个人博客](https://vinga.ml/hugo/)  
[HUGO 官网](https://gohugo.io/)  
[Hugo中文文档](http://www.gohugo.org/)   
[配置 Hugo-- config.tomal参数介绍](http://www.gohugo.org/doc/overview/configuration/)    
[文章格式化支持--Supported Content Formats](https://gohugo.io/content-management/formats/)  
[使用github创建个人主页及项目主页](https://blog.csdn.net/wangjianno2/article/details/78061662)  
[Hugo静态网站生成器中文教程](http://nanshu.wang/post/2015-01-31/)  


