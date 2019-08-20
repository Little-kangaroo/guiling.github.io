---
layout:     post
title:      "Spring源码学习"
subtitle:   "Spring源码学习之万事开头难"
date:       2019-08-20
author:     "GuiLing"
header-img: "img/post-bg-js-module.jpg"
tags:
    - 框架
    - Spring

---



# Spring源码解析一

在第一篇博文中已经给出类Spring容器创建的过程图，从这篇文章开始，将以分析源码的形式进行Spring的学习。  
首先，当我需要用到一个对象的时候，我们会从Spring容器中通过BeanName的形式去获得Bean（常用方式）例如：

    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfig.class);
    Object dog = applicationContext.getBean("dog");

Spring的容器创建过程由此处开始分析：

1. 使用注解进行Spring的开发时，Spring在创建容器之前会首先会注册当前注解类。

2.  然后进入**refresh()** 方法，整个Spring容器的创建过程都在这个方法内完成。

    ​    2.1进行容器刷新前的预处理工作

![](image/spring/2018/12/2p17srss98hhnpi4h3c48dc12j.png)

记录容器开始启动刷新的当前时间，并且将容器状态修改为激活状态，然后进行属性资源的初始化，这里需要注意<font color="#ffc66d" face="DejaVu Sans Mono"><span style="font-size: 18px;">initPropertySources()</span></font>

方法是一个空的实现，这个方法预留给子类去实现，在容器刷新前做一些自定义属性的操作。

**getEnvironment().validateRequiredProperties();**方法在容器刷新前校验被设置为require的属性是否为空，若为空将直接返回异常<font color="#ff9c00">**MissingRequiredPropertiesException**</font><span style="background-color: rgb(239, 239, 239);">异常。</span>

![](image/spring/2018/12/66s3ftvq8ciqvpbua3ne6j5drm.png)

最后创建一个List容器用来存放早期生产的事件当派发器创建完成后将会对放在容器中的事件进行派发

<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'DejaVu Sans Mono';font-size:13.5pt;"><span style="color:#cc7832;">this</span>.<span style="color:#9876aa;">earlyApplicationEvents</span> = <span style="color:#cc7832;">new</span> LinkedHashSet<applicationevent>()<span style="color:#cc7832;">;</span></applicationevent></pre>