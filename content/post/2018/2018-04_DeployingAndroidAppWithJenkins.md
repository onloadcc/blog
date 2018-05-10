+++
title = "使用Jenkins布署Android打包平台"
description = "使用Jenkins布署Android打包平台，并上传到七牛云"
date = "2018-04-13"
categories = ['Android','Tools']
tags = ['Android','Jenkins','七牛云']
thumbnail = ""
+++


　　渠道加的起来越多了，手动打包再上传真是够了。用下Jenkins来进行集成打包。来定个目标：自动进行构建，完成后上传到七牛云上，获取到下载链接。


<!--more-->

### 环境检查下

```
### 操作系统版本
cat /etc/redhat-release　　
CentOS Linux release 7.2.1511 (Core)

### 查看系统内核
uname -a

### 查看磁盘信息
df -h

### 查看指定目录大小
du -sh <目录名>

### cpu信息
cat /proc/cpuinfo

### 内存信息
cat /proc/meminfo

```

### 一  基础SDK安装

##### 1.JDK安装

系统自带的jdk功能少，例如`javac`命令就没有，删除后重新安装。


```
### 删除JDK
rpm -qa | grep jdk
rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.151-5.b12.el7_4.x86_64

### 下载新的
wget --no-cookie --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u161-b12/2f38c3b165be4555a1fa6e98c45e0808/jdk-8u161-linux-x64.tar.gz
tar zxvf jdk-8u161-linux-x64.tar.gz
mv jdk1.8.0_161 /usr/local/jdk1.8.0_161


### 添加`JAVA_HOME`环境变量
vim ~/.bash_profile

JAVA_HOME=/usr/local/jdk1.8.0_161
export JAVA_HOME
export PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$CLASSPATH:$JAVA_HOME/jre/bin

source ~/.bash_profile 

### test
java -version
```

##### 2.Gradle安装

Gradle和AndroidSDK都说用自己电脑的，就不用下载啦，直接拷贝使用。注意拷贝项目使用的Gradle版本。又拷贝了一次和下面的截图不一致。

```
## copy file
scp gradle-4.1-all.zip root@192.168.0.106:/home

## unzip 
unzip gradle-4.1-all.zip

## 添加环境变量
vim ~/.bash_profile

GRADLE_HOME=/usr/local/gradle-4.1
export GRADLE_HOME
export PATH=$GRADLE_HOME/bin:$PATH

source ~/.bash_profile 

## test
gradle -v
```

#### 3.AndroidSDK配置

到官网下载Linux版本的SDK。

```
###Linux版本SDK下载
wget https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip

unzip sdk-tools-linux-3859397.zip

mv ~/tools /usr/local/ANDROID/sdk

###环境变量添加
vim ~/.bash_profile

ANDROID_HOME=/usr/local/ANDROID/sdk
export ANDROID_HOME
export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/tools/bin:$PATH

source ~/.bash_profile
```


```
###显示SDK列表
sdkmanager --list

###安装需要的工具
sdkmanager "ndk-bundle"
sdkmanager "build-tools;25.0.3"
sdkmanager "platforms;android-25"
```

> 注意直接在`tools`目录下运行`android`是无法运行的，必须使用全路径或者把`tools`加到环境变量中才能使用。


#### 4.Git安装

[起步 - 安装 Git](https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)  
#### 5.Groovy安装。默认不安装也可以
[下载](http://groovy-lang.org/download.html)后拷贝至指定目录，配置环境变量。

```
GROOVY_HOME=/Users/hello/Applications/groovy-2.4.15
export GROOVY_HOME
export PATH=$GROOVY_HOME/bin:$PATH
```

### 二  环境搭建

#### 1.服务器[Tomcat](https://tomcat.apache.org/download-80.cgi)安装。下载对应平台的安装包，直接拷贝到自定义目录即可。

```
###本地有就直接拷了
scp /Users/hello/Downloads/apache-tomcat-8.5.30.tar.gz  root@192.168.0.106:/home
###或下载
wget http://www-us.apache.org/dist/tomcat/tomcat-8/v8.5.30/bin/apache-tomcat-8.5.30.tar.gz
```


```
###启动
sudo ./startup.sh
###关闭　
sudo ./shutdown.sh
```


```
###添加自动启动　　
vim /etc/rc.d/rc.local

export JAVA_HOME=/usr/local/jdk1.8.0_161　###必须加
/usr/local/tomcat/bin/startup.sh start
```


#### 2. [Jenkins](https://jenkins.io/download/),到官网下载安装包，这里选择`Generate Java package(.war)`进行安装。

下载后直接把`jenkins.war`文件拷贝到Tomcat的`webapps`目录下。

```
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
mv jenkins.war  /usr/local/tomcat/webapps/
```

启动Tomcat服务器，访问`http://localhost:8080`进行安装配置。

###### 1. 输入密码

 {{< figure src="/img/jenkins/jenkins_setting_1.png" alt="input password" width="600px" height="400px">}}
<p></p>

###### 2. 安装插件，使用默认安装即可

 {{< figure src="/img/jenkins/jenkins_setting_2.png" alt="install plugins" width="600px" height="400px">}}
<p></p>

###### 3. 添加帐户

 {{< figure src="/img/jenkins/jenkins_setting_3.png" alt="add accounts" width="600px" height="400px">}}

<p></p>

###### 4. 配置环境变量

系统设置中添加`ANDROID_HOME`

 {{< figure src="/img/jenkins/jenkins_setting_4_ANDROID_HOME.png" alt="setting android home" width="600px" height="200px">}}
<p></p>	

全局工具配置配置`JDK`和`Gradle`目录

 {{< figure src="/img/jenkins/jenkins_setting_4_JDK.png" alt="setting jdk" width="600px" height="200px">}}

 {{< figure src="/img/jenkins/jenkins_setting_4_Gradle.png" alt="setting gradle" width="600px" height="200px">}}

<p></p>


### 三　创建任务　

##### 1.创建任务

开始创建一个任务，填写名称，选择构建一个自由风格的软件项目。

 {{< figure src="/img/jenkins/jenkins_task_1.png" alt="task 1" width="600px" height="800px">}}

<p></p>

##### 2.源码管理添加

从什么地方获取代码，可使用Git或SVN。

 {{< figure src="/img/jenkins/jenkins_task_2_1.png" alt="task 1" width="600px" height="800px">}}

 {{< figure src="/img/jenkins/jenkins_task_2_2.png" alt="task 1" width="600px" height="800px">}}

<p></p>

##### 3.构建触发器

定义什么情况下触发构建或手动进行构建

 {{< figure src="/img/jenkins/jenkins_task_3.png" alt="task 1" width="600px" height="800px">}}

<p></p>

##### 4.构建环境

 {{< figure src="/img/jenkins/jenkins_task_4_1.png" alt="task 1" width="600px" height="800px">}}

<p></p>

在这里使用第三方插件(Env Injector)进行动态注入变量：读取本地的Properties文件，将变量注入到系统变量中，可在后面直接使用

 {{< figure src="/img/jenkins/jenkins_task_4_2.png" alt="task 1" width="600px" height="800px">}}

<p></p>

##### 5.构建

根据项目选择合适的构建类型，或者执行命令行都可以。

 {{< figure src="/img/jenkins/jenkins_task_5_1.png" alt="task 1" width="600px" height="800px">}}

<p></p>

 {{< figure src="/img/jenkins/jenkins_task_5_2.png" alt="task 1" width="600px" height="800px">}}

<p></p>

##### 6.构建后操作

执行构建完成后需要的操作，可在这里发送邮件通知或作其它操作。
这里指定上传到七牛云上，并获取到完整的下载链接。　　

[Jenkins插件－上传文件到七牛云](https://github.com/onloadcc/qiniu-file)，详细使用请点击查看。

 {{< figure src="/img/jenkins/jenkins_task_6_1.png" alt="task 1" width="600px" height="800px">}}

<p></p>

 {{< figure src="/img/jenkins/jenkins_task_6_2.png" alt="task 1" width="600px" height="800px">}}

<p></p>


构建后执行脚本插件[PostBuildScript Plugin](https://wiki.jenkins.io/display/JENKINS/PostBuildScript+Plugin),可执行shell,groovy,gradle等脚本。

项目之间传递参数插件[Parameterized Trigger Plugin](https://wiki.jenkins.io/display/JENKINS/Parameterized+Trigger+Plugin)  
可在项目构建完成后启动其它项目时传递参数过去。
  

### ？待解决

Q:　jenkins执行groovy脚本时权限处理？如何直接执行不需要批准？　

A:　暂时解决方案：把groovy脚本放在单独路径，不放在git管理库中。任务填写时不勾选`Run in Groovy Sandbox`，第一次执行时批准一次，后续会默认执行。


默认执行时会报错，使用的有读写文件的api。
```
[PostBuildScript] - Executing post build scripts.
[PostBuildScript] - Problem occurred: org.jenkinsci.plugins.scriptsecurity.scripts.UnapprovedUsageException: script not yet approved for use
	at org.jenkinsci.plugins.scriptsecurity.scripts.ScriptApproval.using(ScriptApproval.java:466)
	at org.jenkinsci.plugins.scriptsecurity.sandbox.groovy.SecureGroovyScript.evaluate(SecureGroovyScript.java:343)
	at org.jenkinsci.plugins.postbuildscript.service.GroovyScriptExecutor.execute(GroovyScriptExecutor.java:49)
	at org.jenkinsci.plugins.postbuildscript.service.GroovyScriptPreparer.evaluateScript(GroovyScriptPreparer.java:55)
	at org.jenkinsci.plugins.postbuildscript.service.GroovyScriptPreparer.evaluateCommand(GroovyScriptPreparer.java:86)
	at org.jenkinsci.plugins.postbuildscript.processor.Processor.processScriptFiles(Processor.java:139)
	at org.jenkinsci.plugins.postbuildscript.processor.Processor.processScripts(Processor.java:93)
	at org.jenkinsci.plugins.postbuildscript.processor.Processor.process(Processor.java:83)
	at org.jenkinsci.plugins.postbuildscript.processor.Processor.process(Processor.java:77)
	at org.jenkinsci.plugins.postbuildscript.PostBuildScript.perform(PostBuildScript.java:114)
	at hudson.tasks.BuildStepMonitor$1.perform(BuildStepMonitor.java:20)
	at hudson.model.AbstractBuild$AbstractBuildExecution.perform(AbstractBuild.java:744)
	at hudson.model.AbstractBuild$AbstractBuildExecution.performAllBuildSteps(AbstractBuild.java:690)
	at hudson.model.Build$BuildExecution.post2(Build.java:186)
	at hudson.model.AbstractBuild$AbstractBuildExecution.post(AbstractBuild.java:635)
	at hudson.model.Run.execute(Run.java:1752)
	at hudson.model.FreeStyleBuild.run(FreeStyleBuild.java:43)
	at hudson.model.ResourceController.execute(ResourceController.java:97)
	at hudson.model.Executor.run(Executor.java:429)

Build step 'Execute Scripts' changed build result to FAILURE
Build step 'Execute Scripts' marked build as failure
```


### 参考

[Android Jenkins+Git+Gradle 持续集成 ](https://juejin.im/entry/585a6b6bb123db006597195a)  
[使用Jenkins搭建iOS/Android持续集成打包平台](http://debugtalk.com/post/iOS-Android-Packing-with-Jenkins/)   
[浅析 Jenkins 插件开发](https://www.ibm.com/developerworks/cn/java/j-lo-jenkins-plugin/)  
[Jenkins添加构建后shell cmd的应用插件--PostBuildScript plugin插件](http://www.cnblogs.com/xindong/p/5364970.html)  
[Jenkins项目实战之-Android基于Gradle参数化打不同环境安装包（二）](https://blog.csdn.net/u011541946/article/details/78278494)  
[Jenkins项目实战之](https://blog.csdn.net/u011541946/article/category/7175041)  
[Jenkins插件之环境变量插件EnvInject](http://www.cnblogs.com/itech/archive/2011/11/18/2254188.html)  
[Jenkins参数化构建（三）之 Jenkins从文件中读取运行参数--Extended Choice Parameter](http://www.cnblogs.com/xiaochengzi/p/8251805.html)  
[jenkins中的环境变量](https://my.oschina.net/donhui/blog/530715)  
[Jenkins Plugin 获取变量的问题。](https://testerhome.com/topics/8311)  
[Jenkins学习（四）job界面详解](https://blog.csdn.net/taishanduba/article/details/61423121)  
[Jenkins 进程内脚本批准--执行Groovy脚本权限处理](https://www.w3cschool.cn/jenkins/jenkins-85xp28mk.html)  



