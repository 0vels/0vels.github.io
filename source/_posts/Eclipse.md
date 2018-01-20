---
title: Eclipse
date: 2017-11-18 22:23:05
tags: Eclipse
categories: Eclipse
---

这是一篇关于Eclipse的吐槽，从开始自学安卓就一直用的Android studio，虽然Android studio前几个版本用起来很卡，但是在各种配置环境方面还算比较省心，现在来到新公司由于项目的原因必须用Eclipse，所以就开始了这段Eclipse的踩坑之路。
 <!-- more --> 

    if(导出本文的时候格式不会出错)
            本文来自Notion网页版；
    else
            本文来自Sublime;

## 1.先把Eclipse安装好以后必须改的设置写在这里吧

- Eclipse自动补全的设置

  Windows——>Preferences——>Java—>Editor—>Content Asist，把Auto activation triggers for Java选项下的Auto activation triggers for Java "."改为：

      .abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ

- Eclipse的字体大小设置

  Window -> Preferences -> General -> Appearance -> Colors and Fonts -> Basic -> Text Font -> Edit，现在用的“小三”。

- 其他的想起来了再写

## 2.之前遇到的问题

之前遇到的问题没有记下来，所以。。。下次遇到了还是会被坑。后面会慢慢补上

## 3.Unknown error: Unable to build: the file dx.jar was not loaded from the SDK folder!

遇到这个问题先确认一下dx.jar文件是否存在（当然存在！）

在一个国外论坛看到解决方法是把Android SDK Build-tools 26删掉，换成25。

现在时间是2017-11-18，最新的版本是27.0.1，经测试删掉27换成26并不能解决问题。换成25就可以了。ok，下一题。

## 4.v7-appcompat\res\values-v21\themes_base.xml:139: error: Error: No resource found that matches the given name

这个问题据说是Android SDK Manger的Extras里面没有安装Android Support Library 造成的，官方地址里面已经没有这个选项了，当然国内的各个镜像地址里面也没有，目前Library已经被集成到Android Support Repository里了，在SDK\extras\android\m2repository\com\android\support文件夹下，但是Eclipse识别不了这个目录下的文件，所以尝试下载v7包放在SDK\extras\android下面，但是问题没有解决，甚至把v7的jar包放在项目libs目录下都没用。然后。。。。。问题3解决了之后，这个问题也消失了。 ？？？？？？？？？？？？

## 5.SDK下载

之前国内有很多SDK的镜像，现在很多都不能用了，在这个网站只看到一个镜像地址

[AndroidDevTools](http://www.androiddevtools.cn/)

郑州大学开源镜像站: `mirrors.zzu.edu.cn`  端口：80

现在每个人都有飞机，可以在Android SDK Manger -> Tools -> Options里设置HTTP Proxy Server:127.0.0.1，HTTP Proxy Port:你的飞机本地监听端口，这样就可以直接访问官方SDK地址了。

## 6.CTRL+左键看不到源代码

以前一直以为这是编译器自带的基本功能，现在才知道是Gradle的功劳。

这里又要吐槽一下：

> Eclipse的设计思想是：一切皆插件。

Eclipse想用这个功能只能装反编译插件。先下载JAD，

 [http://blog.csdn.net/qq_27489007/article/details/77050016](http://blog.csdn.net/qq_27489007/article/details/77050016) 

明天继续写，好困。