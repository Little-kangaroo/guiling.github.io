---
layout:     post
title:      "Spring源码学习(三)"
subtitle:   "Spring源码学习之柳暗花明又一村"
date:       2019-08-21
author:     "GuiLing"
header-img: "img/post-bg-js-module.jpg"
tags:
    - 框架
    - Spring

---

## Spring源码分析（三）

- 准备BeanFactory

上一篇讲到对BeanFactory进行刷新有两种不同的操作，在BeanFactory刷新完成之后，就要进行BeanFactory使用前的准备了。

用prepareBeanFactory(beanFactory);方法对BeanFactory做使用前的准备，实际上就是给BeanFactory设置相应的属性，设置BeanFactory的上下文。

![img](\image\spring\2018\12\7nndu91d6cgsrpdpgdh068u1fl.png)

在BeanFactory的准备阶段做了很多事情，我们逐步的进行分析。

首先，给BeanFactory设置了所需要使用的类加载器，表达式解析器，编辑注册器。

```
beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
```

是比较重要的，他给BeanFactory设置了一个PostProcess后置处理器，**ApplicationContextAwareProcessor**是一个实现了BeanPostprocess的类，由BeanPostProcess的特性可知这个**ApplicationContextAwareProcessor** 中的两个主要方法**postProcessBeforeInitialization和postProcessAfterInitialization**将会在自定义的Bean的init方法执行前后去执行，postProcessAfterInitialization方法并没有过多的操作，主要的操作在**postProcessBeforeInitialization**方法中![img](\image\spring\2018\12\7ur95mi9t6g5cput1nguhs8abp.png)

在前置方法中执行了inVokeAwareInterfaces方法![img](\image\spring\2018\12\1tjvu1ukjchu2pum2kdk10e71s.png)

由此可见在Bean的Init方法执行前，所有的实现了上述接口的Bean都被注入了额外的属性。在执行了invokeInterfaces方法后这些Bean已经不是普通的Bean了，所以再做Bean的依赖注入是要忽略他们，所以下面BeanFactory做了一部分忽略设置，相对性的还有一些必不可少的解析依赖，例如当注入了BeanFactory.class的解析依赖之后，在进行Bean的注入的时候一旦检测到属性为BeanFactory类型就会将BeanFactory实例注入进来。