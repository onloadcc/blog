+++
title = "Android 反编译与混淆"
description = "Android 反编译与混淆"
date = "2017-06-02"
categories = ["Android"]
tags = ["Android","Proguard"]
thumbnail = ""
+++


<!--more-->

### 反编译工具

[dex2jar--dex文件转为jar文件](https://sourceforge.net/projects/dex2jar/?source=typ_redirect)  
 
```
d2j-dex2jar classes.dex  		  
```

[jd-gui-查看jar文件](http://jd.benow.ca/)  

[ApkTool-还原Apk中资源文件](https://ibotpeaches.github.io/Apktool/install/)  

```
apktool.bat d test.apk
```

> d 代表 decode  
> 可能异常：以前使用老版本`apktool`操作过，删除缓存文件`C:\Users\pc\AppData\Local\apktool\framework\1.apk`即可。


### 混淆

#### 1. 在 app model 的 build.gradle 文件中开启混淆

```
  buildTypes {
    release {
      //资源文件压缩
      shrinkResources true
      //代码开启混淆
      minifyEnabled true
      //混淆文件位置： 第一个：系统默认混淆文件。第二个：自定义混淆文件
      proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    debug {
      //资源文件压缩
      shrinkResources true
      //代码开启混淆
      minifyEnabled true
      //混淆文件位置： 第一个：系统默认混淆文件。第二个：自定义混淆文件
      proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
  }
```

#### 2. 配置自定义的混淆文件

	混淆需要排除的文件：

	- 通用排除的
		- 四大组件，自定义的Application  
		- native方法
		- Activity中方法参数是View的方法(`layout`中的`onclick`事件)
		- 枚举类中枚举相关方法
		- 自定义控件（继承自View）
		- Parcelable序列化类
		- Serializable序列化类
		- webView
	- 自己项目中使用的
		- 反射类
		- js交互
		- 实体类（Json解析） 或添加`@keep`注解
	- 第三方依赖库		


```
# Add project specific ProGuard rules here.
# By default, the flags in this file are appended to flags specified
# in D:\AppData\Local\Android\sdk/tools/proguard/proguard-android.txt
# You can edit the include path and order by changing the proguardFiles
# directive in build.gradle.
#
# For more details, see
#   http://developer.android.com/guide/developing/tools/proguard.html

# Add any project specific keep options here:

# If your project uses WebView with JS, uncomment the following
# and specify the fully qualified class name to the JavaScript interface
# class:
#-keepclassmembers class fqcn.of.javascript.interface.for.webview {
#   public *;
#}

 #Uncomment this to preserve the line number information for
 #debugging stack traces.
-keepattributes SourceFile,LineNumberTable

 #If you keep the line number information, uncomment this to
 #hide the original source file name.
-renamesourcefileattribute SourceFile

# 以上为系统默认

############################################
#
# 对于一些基本指令的添加
#
#############################################
# 代码混淆压缩比，在0~7之间，默认为5，一般不做修改
-optimizationpasses 5

# 混合时不使用大小写混合，混合后的类名为小写
-dontusemixedcaseclassnames

# 指定不去忽略非公共库的类
-dontskipnonpubliclibraryclasses

# 这句话能够使我们的项目混淆后产生映射文件
# 包含有类名->混淆后类名的映射关系
-verbose

# 指定不去忽略非公共库的类成员
-dontskipnonpubliclibraryclassmembers

# 不做预校验，preverify是proguard的四个步骤之一，Android不需要preverify，去掉这一步能够加快混淆速度。
-dontpreverify

# 保留Annotation不混淆
-keepattributes *Annotation*,InnerClasses

# 避免混淆泛型 ？ 不知道用途
-keepattributes Signature

# 抛出异常时保留代码行号
#-keepattributes SourceFile,LineNumberTable

# 指定混淆是采用的算法，后面的参数是一个过滤器
# 这个过滤器是谷歌推荐的算法，一般不做更改
#-optimizations !code/simplification/cast,!field/*,!class/merging/*


#############################################
#
# Android开发中一些需要保留的公共部分
#
#############################################

# 保留我们使用的四大组件，自定义的Application等等这些类不被混淆
# 因为这些子类都有可能被外部调用
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Appliction
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class * extends android.view.View
-keep public class com.android.vending.licensing.ILicensingService


# 保留support下的所有类及其内部类
-keep class android.support.** {*;}

# 保留继承的
-keep public class * extends android.support.v4.**
-keep public class * extends android.support.v7.**
-keep public class * extends android.support.annotation.**

# 保留R下面的资源
-keep class **.R$* {*;}

# 保留本地native方法不被混淆
-keepclasseswithmembernames class * {
    native <methods>;
}

# 保留在Activity中的方法参数是view的方法，
# 这样以来我们在layout中写的onClick就不会被影响
-keepclassmembers class * extends android.app.Activity{
    public void *(android.view.View);
}

# 保留枚举类不被混淆
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

# 保留我们自定义控件（继承自View）不被混淆
-keep public class * extends android.view.View{
    *** get*();
    void set*(***);
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}

# 保留Parcelable序列化类不被混淆
-keep class * implements android.os.Parcelable {
    public static final android.os.Parcelable$Creator *;
}

# 保留Serializable序列化的类不被混淆
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    !static !transient <fields>;
    !private <fields>;
    !private <methods>;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}

# 对于带有回调函数的onXXEvent、**On*Listener的，不能被混淆
-keepclassmembers class * {
    void *(**On*Event);
    void *(**On*Listener);
}

# webView处理，项目中没有使用到webView忽略即可
-keepclassmembers class fqcn.of.javascript.interface.for.webview {
    public *;
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String);
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.webView, jav.lang.String);
}

# 移除Log类打印各个等级日志的代码，打正式包的时候可以做为禁log使用，这里可以作为禁止log打印的功能使用
# 记得proguard-android.txt中一定不要加-dontoptimize才起作用
# 另外的一种实现方案是通过BuildConfig.DEBUG的变量来控制
#-assumenosideeffects class android.util.Log {
#    public static int v(...);
#    public static int i(...);
#    public static int w(...);
#    public static int d(...);
#    public static int e(...);
#}

#############################################
#
# 项目中特殊处理部分
#
#############################################

#-----------处理反射类---------------



#-----------处理js交互---------------



#-----------处理实体类---------------
# 在开发的时候我们可以将所有的实体类放在一个包内，这样我们写一次混淆就行了。
#-keep public class com.ljd.example.entity.** {
#    public void set*(***);
#    public *** get*();
#    public *** is*();
#}
-keep class com.gc.caiba.bean.** { *; }

#-----------处理第三方依赖库---------

# ButterKnife 7
-keep class butterknife.** { *; }
-dontwarn butterknife.internal.**
-keep class **$$ViewBinder { *; }

-keepclasseswithmembernames class * {
    @butterknife.* <fields>;
}

-keepclasseswithmembernames class * {
    @butterknife.* <methods>;
}


## Square Picasso specific rules ##
## https://square.github.io/picasso/ ##
-dontwarn com.squareup.okhttp.**


## GSON 2.2.4 specific rules ##
# Gson uses generic type information stored in a class file when working with fields. Proguard
# removes such information by default, so configure it to keep all of it.
#-keepattributes Signature

# For using GSON @Expose annotation
#-keepattributes *Annotation*
-keepattributes EnclosingMethod

# Gson specific classes
-keep class sun.misc.Unsafe { *; }
-keep class com.google.gson.stream.** { *; }


#okhttputils
-dontwarn com.zhy.http.**
-keep class com.zhy.http.**{*;}

# OkHttp
#-keepattributes Signature
#-keepattributes *Annotation*
-keep class okhttp3.** { *; }
-keep interface okhttp3.** { *; }
-dontwarn okhttp3.**

# Okio
#-keep class sun.misc.Unsafe { *; }
-dontwarn java.nio.file.*
-dontwarn org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
-dontwarn okio.**
#-dontwarn [class_filter] 声明不输出那些未找到的引用和一些错误，但续混淆。配置中的class_filter是一串正则表达式，被匹配到的类名相关的警告都不会被输出出来。


# libs 目录当中的
# common.jar
#wheelpckker.jar
#-keep class cn.qqtheme.framework.** { *; }


# tbs_sdk
-dontwarn com.tencent.**
-keep class com.tencent.**{*;}
-keep interface com.tencent.** { *; }

# library model
#-keep class com.shizhefei.** { *; }
```


#### 3. Apk生成后，对应的 build 目录会产生日志文件。如：`app/build/outputs/mapping/debug/mapping.txt`

    dump.txt 	描述APK文件中所有类的内部结构  
    mapping.txt  提供混淆前后类、方法、类成员等的对照表  
    resources.txt    
    seeds.txt  列出没有被混淆的类和成员  
    usage.txt  列出被移除的代码  


#### 4. 查看错误日志
	
  	使用`proguardgui`工具。位于sdk目录下，如`D:\develop\AndroidSDK\tools\proguard\bin\proguardgui.bat`  
	
    打开后点击左边最下角 `ReTrace` 选项： `Mapping file`中选择打包 apk 时生成的 `mapping.txt` 文件，将错误日志粘贴至 `Obfuscated stack trace` 编辑框中，点击 `ReTrace!`按纽即可。


### 参考

[Android安全攻防战，反编译与混淆技术完全解析（上）](http://android.jobbole.com/82641/)  
[Android安全攻防战，反编译与混淆技术完全解析（下）](http://android.jobbole.com/82644/)  
[Android Proguard(混淆)](http://webcache.googleusercontent.com/search?q=cache:rh6rF2kTQx8J:www.jianshu.com/p/60e82aafcfd0+&cd=1&hl=en&ct=clnk&gl=kh)  
[写给 Android 开发者的混淆使用手册](https://webcache.googleusercontent.com/search?q=cache:y1lIFk5O-TAJ:https://www.diycode.cc/topics/380+&cd=4&hl=en&ct=clnk&gl=kh)  
[Android Studio混淆模板及常用第三方混淆(看了都说好)](http://webcache.googleusercontent.com/search?q=cache:rx0QPpjuZKUJ:www.jianshu.com/p/f9438603e096+&cd=2&hl=en&ct=clnk&gl=kh)  
[Shrink Your Code and Resources](https://developer.android.com/studio/build/shrink-code.html#shrink-resources)  
[ProGuard manual](https://www.guardsquare.com/en/proguard/manual/gradle)  
[android-proguards- 第三方开源库混淆文件参考](https://github.com/yongjhih/android-proguards)  
[混淆（Proguard）用法](https://www.jianshu.com/p/60e82aafcfd0)   


