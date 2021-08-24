---
title: Error starting static Resources
date: 2021-03-09 14:26:00
tag: Tomcat
categories: Bug
toc: true
thumbnail: /thumbnails/Bug/Bug.png
---

启动静态资源错误

<!--more-->

## 运行环境

* Apache Tomcat/7.0.81
* Eclipse Luna Release (4.4.0)
* jdk1.8.0_102
* apache-maven-3.5.3

## Bug描述

> 基于Maven搭建的Web项目Dome启动时，在Eclipse的Console窗口打印启动日志处出现`Error starting static Resources`，项目仍可正常运行。

## 具体Bug

![Error starting static Resources](Error%20starting%20static%20Resources/1.png)

## Bug分析

1. 分析异常信息

   >  `java.lang.IllegalArgumentException: Document base D:\Learn\code\Eclipse_workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp1\wtpwebapps\alipay.trade.page.pay-JAVA-UTF-8 does not exist or is not a readable directory`

   上述异常信息表示文件`alipay.trade.page.pay-JAVA-UTF-8`不存在或不是一个可读目录

   > `org.apache.catalina.LifecycleException: Failed to start component [StandardEngine[Catalina].StandardHost[localhost].StandardContext[/alipay.trade.page.pay-JAVA-UTF-8]]`

   上述异常信息表示启动组件`alipay.trade.page.pay-JAVA-UTF-8`失败

   > `Error deploying configuration descriptor D:\Learn\code\Eclipse_workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp1\conf\Catalina\localhost\alipay.trade.page.pay-JAVA-UTF-8.xml`

   上述异常信息表示部署配置`alipay.trade.page.pay-JAVA-UTF-8.xml`文件的描述符错误

2. 根据上述分析，可初步判断为`alipay.trade.page.pay-JAVA-UTF-8.xml`该XML文件配置的静态资源不存在，导致无法启动`alipay.trade.page.pay-JAVA-UTF-8`组件

3. 经具体查看发现确实是`alipay.trade.page.pay-JAVA-UTF-8.xml`该XML文件配置的静态资源不存在，导致无法启动`alipay.trade.page.pay-JAVA-UTF-8`组件

   * Tomcat下只显示部署了`maven-dome-web`

     ![](Error%20starting%20static%20Resources/4.png)

   * 而异常信息中的指定XML文件路径下却有4个项目的XML配置文件

     ![](Error%20starting%20static%20Resources/3.png)

   * 查看`alipay.trade.page.pay-JAVA-UTF-8.xml`

     ![](Error%20starting%20static%20Resources/5.png)

   * 根据配置文件中的路径查找，发现只有`maven-dome-web`，并没有`alipay.trade.page.pay-JAVA-UTF-8`

     ![](Error%20starting%20static%20Resources/2.png)

## 解决办法

将`D:\Learn\code\Eclipse_workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp1\conf\Catalina\localhost\alipay.trade.page.pay-JAVA-UTF-8.xml`同路径下的除`maven-dome-web.xml`文件以外的其他XML文件删除即可。

## 补充

后续发现上诉解决办法无法从根本上解决问题，每当有新的未加入Tomcat的项目加入Tomcat启动时，之前被删除的XML文件又恢复了，`Error starting static Resources`随之再次出现，通过查找资料发现按以下步骤可根本性解决问题。实际上之前删除的XML文件就是由此处配置的，当加入新的项目时，Tomcat会重新加载生成这些XML配置文件，所以出现了之前被删除的文件又恢复的情况。

![](Error%20starting%20static%20Resources/6.png)

![](Error%20starting%20static%20Resources/7.png)

---

**学习所得，资料、图片部分来源于网络，如有侵权，请联系本人删除。**

**才疏学浅，若有错误或不当之处，可批评指正，还请见谅！**