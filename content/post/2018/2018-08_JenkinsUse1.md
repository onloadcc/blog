+++
title = "Jenkins使用记录"
description = "Jenkins插件使用"
date = "2018-08-17"
categories = ['Jenkins','Tools']
tags = ['Jenkins','Tools','Plugins']
thumbnail = ""
+++


 介绍日常中`Jenkins`使用记录。

<a href="{{< ref "post/2018/2018-08_JenkinsUse1.md#1-jenkins动态化生成全局参数" >}}">1. jenkins动态化生成全局参数</a>



<!--more-->


## 1.jenkins动态化生成全局参数 



jenkins根据用户输入生成动态参数，并提供给全局使用。

### 安装插件

[EnvInject Plugin](https://wiki.jenkins.io/display/JENKINS/EnvInject+Plugin)  

### 使用

1 . 在参数化构建中添加需要判断的值，提供给后续判断

 {{< figure src="/img/jenkinsuse/jenkins_use_dynamic_parameters_step1.png" alt="jenkins use dynamic parameters step1" width="700px" height="200px">}}

<p></p>	

2 . 勾选`Inject environment variables to the build process`，并在`Groovy Script`输入判断内容。

> `Groovy Script`脚本中返回一个`map`集合，对应的`key`即可在后续中当作全局变量使用。可参考帮助。

> `Groovy`中引用全局变量时和其它地方不一样，直接使用变量名。
如`isTestZone`变量，使用`isTestZone`就可以，不能用`${isTestZone}`。

> jenkins中全局变量为字符串类型，没有布尔类型，判断时需要注意。


```
println "test: "+isTestZone

def isTest = isTestZone
println "test: " +isTest

if (isTest.equals("true")) {
return [URL:"http://debug.com"]
} else {
return [URL:"http://nodebug.com"]
}
```

 {{< figure src="/img/jenkinsuse/jenkins_use_dynamic_parameters_step2.png" alt="jenkins use dynamic parameters step1" width="700px" height="200px">}}

<p></p>	

3 . 在构建中模拟获取变量，是否获取到。

 {{< figure src="/img/jenkinsuse/jenkins_use_dynamic_parameters_step3.png" alt="jenkins use dynamic parameters step1" width="700px" height="200px">}}

<p></p>	

### `EnvInject Plugin`获取`properties`文件中变量

在`Properties File Path`中填入文件位置即可。

 {{< figure src="/img/jenkinsuse/jenkins_use_dynamic_parameters_properties.png" alt="jenkins use dynamic parameters step1" width="700px" height="200px">}}

<p></p>	

示例`config.properties`文件内容，APP版本号:
```
VersionName=1.0.1
```









