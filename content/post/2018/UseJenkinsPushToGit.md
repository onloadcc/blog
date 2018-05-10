+++
title = "Jenkins实现Git提交"
description = "Jenkins实现Git提交。push changes to github after jenkins build completes"
date = "2018-05-10"
categories = ['Jenkins','Tools']
tags = ['Jenkins','Git']
thumbnail = ""
+++

    最近项目需求需要在jenkins构建成功后把生成的指定文件上传到git仓库中去，以便后续使用。到网上找没有个完整的步骤，就整理下步骤，方便后续使用。用到的插件有`Git Publisher`，git相关插件是默认安装好的。

<!--more-->


### 1. 新建一个项目，源码管理中选择Git，并配置好源码库信息。

### 2. 然后处理需要更改的文件或添加文件。

### 3. 在构建步骤中添加`增加构建步骤`，`Execute shell`。在`Command`中填写：

```
git add *   #添加指定文件
git commit -m "jenkins add new file" #文件提交到本地仓库中
```


 {{< figure src="/img/jenkins/jenkins_add_git_shell.png" alt="add file shell" width="700px" height="200px">}}

<p></p>	

### 4. 在构建后操作中使用`Git Publisher`插件进行代码提交。

 {{< figure src="/img/jenkins/jenkins_git_publisher.png" alt="git publisher setting" width="700px" height="800px">}}

<p></p>


