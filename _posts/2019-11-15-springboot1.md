---
layout:     post
title:      "正面硬刚SpringBoot源码之第一行代码"
subtitle:   "第一行代码"
date:        2019-11-15
author:     "GuiLing"
header-img: "img/post-bg-js-module.jpg"
tags:

   - SpringBoot
---
- ## SpringBootApplication

1. 使用 `@SpringBootApplication` 注解，标明是 Spring Boot 应用。通过它，可以开启自动配置的功能。

![Xnip2019-11-17_16-01-07](/img/spring/2019/11-17/Xnip2019-11-17_16-01-07.jpg)

2.调用 `SpringApplication#run(Class... primarySources)` 方法，启动 Spring Boot 应用。

接下来看SpringBoot的 ***启动过程***关于SpringBoot的注解会在后续文章中逐个解析。

![image-20191117160857940](/img/spring/2019/11-17/image-20191117160857940.png)

如注释描述它来提供启动 Spring 应用的功能```org.springframework.boot.SpringApplication```

- 大多数情况下我们都是使用它的静态方法

![image-20191117161414199](/img/spring/2019/11-17/image-20191117161414199.png)

- 前两个静态方法最终都是调用第三个静态方法，首先看一下第三个静态方法的实现逻辑

  - 首先，创建一个 SpringApplication 对象。
  - 然后，调用 `SpringApplication#run(Class primarySource, String... args)` 方法，运行 Spring 应用。

- ### SpringApplication的构造器

  ![image-20191117162452698](/img/spring/2019/11-17/image-20191117162452698.png)

- SpringApplication的参数较多我们先看构造器中提到的几个
  - `resourceLoader` 属性，资源加载器。可以暂时不理解，在后续Spring源码解析中会详细介绍
  - `primarySources` 属性，主要的 Java Config 类的数组。在文初提供的示例，就是DemoApplication 类
  - `webApplicationType` 属性，调用 `WebApplicationType#deduceFromClasspath()` 方法，通过 classpath ，判断 Web 应用类型。
    - 具体的原理是，是否存在指定的类，WebApplicationType上的方法添加了注释，直接瞅一眼就明白了。
    - 这个属性，在下面的 `#createApplicationContext()` 方法，将根据它的值（类型），创建不同类型的 ApplicationContext 对象，即 Spring 容器的类型不同。

![image-20191117162941404](/img/spring/2019/11-17/image-20191117162941404.png)

- - `initializers` 属性，ApplicationContextInitializer 数组。
  - 通过 `#getSpringFactoriesInstances(Class type)` 方法，进行获得 ```ApplicationContextInitializer``` 类型的对象数组，详细的解析，见

```getSpringFactoriesInstances``` 

![image-20191117163703191](/img/spring/2019/11-17/image-20191117163703191.png)

- - listeners` 属性，ApplicationListener 数组。
    - 也是通过 `#getSpringFactoriesInstances(Class type)` 方法，进行获得 ApplicationListener 类型的对象数组。
    - ![image-20191117163827348](/img/spring/2019/11-17/image-20191117163827348.png)

- - `mainApplicationClass` 属性，调用 `#deduceMainApplicationClass()` 方法，获得是调用了哪个 `#main(String[] args)` 方法

    - 这个 `mainApplicationClass` 属性，没有什么逻辑上的用途，主要就是用来打印下日志，说明是通过这个类启动 Spring 应用的。

    ![image-20191117164158221](/img/spring/2019/11-17/image-20191117164158221.png)

- ### getSpringFactoriesInstances

  - `#getSpringFactoriesInstances(Class type)` 方法，获得指定类对应的对象们

  ![image-20191117165339565](/img/spring/2019/11-17/image-20191117165339565.png)

  - - 调用 `SpringFactoriesLoader#loadFactoryNames(Class factoryClass, ClassLoader classLoader)` 方法，加载指定类型对应的，在 `META-INF/spring.factories` 里的类名的数组。
    - 在 `META-INF/spring.factories`文件中，会以 KEY-VALUE 的格式，配置每个类对应的实现类们
    - 调用 `#createSpringFactoriesInstances(Class type, Class[] parameterTypes, ClassLoader classLoader, Object[] args, Set names)` 方法，创建对象们

![image-20191117165746479](/img/spring/2019/11-17/image-20191117165746479.png)

## Run方法解析（正面硬刚的时候到了，集中注意带好安全帽）

![image-20191117171155417](/img/spring/2019/11-17/image-20191117171155417.png)

