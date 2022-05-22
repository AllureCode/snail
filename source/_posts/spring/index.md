---
title: Spring面试高频题目
tags:
  - Spring 
categories:
  - 程序员的春天
---
# Spring面试高频题目


## 谈谈你对Spring IOC的理解

>`IOC`：控制反转，把创建对象交给Spring帮我们管理。之前我们可能会通过new的方式去创建对象，但是Spring提供的IOC可以帮我们完成这件事情。
>
>`DI`：依赖注入，是IOC的具体实现。将对应的属性通过@Autowrite 、@Resource、@populateBean方法来完成属性注入。
>
>`容器`：IOC相当于Spring的容器。帮我们进行对象的创建管理。容器主要是进行对象的存储，在Spring中使用map结构来进行对象存储，并且Spring提供了三级缓存。`singletonObjects 存储完整对象`、`earlySingletonObjects 存放半成品对象`、`singletonFactories 存放lambda表达式和对象名称映射`,整个bean生命周期都是由容器帮我们进行管理。

## 简单描述一下bean的生命周期

> 1.实例化bean对象，通过反射生成的。在源码中是通过createBeanInstance方法进行实例化。
>
> 2.当bean对象创建完成之后，需要对bean对象进行属性填充。调用popularBean方法完成，中间会涉及到循环依赖的问题。
>
> 3.向bean对象中设置容器属性，调用invokeAwareMethods方法将容器属性设置到具体的bean对象中。
>
> 4.调用beanPostProcessor中的前置处理方法进行bean对象的拓展，如ApplicationContextPostProcessor。
>
> 5.调用invokeInitMethods方法来完成初始化方法调用，在此方法中需要判断当前bean对象是否实现了InitialzingBean接口，如果实现了调用afterPropertiesSet方法来进行最后设置bean对象。
>
> 6.调用beanPostProcessor后置处理方法，完成对bean对象的后置处理工作。aop就是在此处实现的，实现的接口名称叫做AbstractAutoProxyCreator。
>
> 7.获取到完整的对象，通过getBean方法去进行对象的获取。
>
> 8.当对象使用完成之后，容器在关闭的时候，会销毁对象。首先会判断是否实现了DispoableBean接口，然后去调用destoryMethod方法。

## beanFactory 和 FactoryBean 有什么区别

> beanFactory 和FactoryBean都可以进行bean对象的创建，只不过二者的创建方式不同。
>
> 当使用beanFactory创建bean对象的时候，必须严格遵守bean生命周期，经过一系列繁杂的流程之后创建出bean对象。
>
> 当使用FactoryBean创建bean对象的时候，可以支持用户自定义bean的创建流程，不需要按照bean生命周期进行创建，此接口包含了三个方法：isSingleton 是否为单例对象、getObjectType 获取对象类型、getObject 次方法中可以自己创建对象，使用new的方式或者使用代理的方式都可以。用户可以按照自己的需要去创建bean对象。

## spring 中有哪些设计模式

>`1.单例模式：bean的创建`
>
>`2.工厂模式：beanFactory`
>
>`3.模版方法：OnRefresh、postProcessorBeanFactory`
>
>`4.观察者模式:lister、event`
>
>`5.适配器模式：Adapter`
>
>`6.装饰者模式：BeanWrapper`
>
>`7.责任链：aop中使用到`
>
>`8.代理模式：aop动态代理`
>
>`9.策略者：配置文件解析`

## applicationContext与BeanFactory区别

> BeanFoactory是spring的root接口、提供了基本的方法和约束。
>
> applicationContext是对BeanFactory接口的实现，拓展了beanFactory接口并且提供了更多的api调用。

## 谈谈你对循环依赖的理解

>1.什么是循环依赖：A依赖B、B依赖A
>
>spring中的bean创建都是需要经过初始化和实例化过程（属性填充），通过将对象状态分开，存储半成品对象和成品对象的方式，来分别进行初始化和实例化，成品对象存放在一级缓存中，半成品对象存放在二级缓存中。
>
>2.只有一级缓存可以吗？
>
>不可以。一个缓存存储的话会把成品对象和半成品对象混合在一起，而半成品对象是无法暴露给外部使用。
>
>3.只有二级缓存可以吗？
>
>如果整个应用程序不涉及aop的存在，那么使用二级缓存可以解决循环依赖的问题，如果aop存在，那么必须使用三级缓存来处理。
>
>4.为什么需要三级缓存？
>
>三级缓存的value是一个函数式接口，只有在调用getObject方法的时候才会去调用里面存储的labmda表达式，存在的意义是确保整个容器在运行中同名的bean对象只有一个。

## Spring AOP底层的实现

>aop是ioc的一个拓展点，先有ioc再有aop。aop的实现是在beanPostProcessor方法中进行实现。
>
>aop底层是使用动态代理实现，通过jdk或者cglib的方式生成代理对象。

## Spring的事务是如何进行回滚的

>spring的事物是由aop实现，首先要生成具体的代理对象，然后按照aop的整套流程来执行具体的操作。正常情况下要通过通知来完成核心功能，但是事务不是通过通知来实现的，而是通过一个TransationInterceptor实现的，然后调用invoke方法执行具体逻辑。
>
>1.在准备阶段，会先解析各个方法上事务相关属性，根据具体的属性判断是否开启事务。
>
>2.当需要开启事务的时候，获取数据库连接，关闭自动提交功能，开启事务。
>
>3.执行具体sql逻辑操作。
>
>4.在操作过程中如果出现异常情况，那么会通过completetransationAfterTrowing来完成事务的回滚操作，回滚的具体逻辑是通过调用doRollback方法实现，实现的时候也是需要先获取对应连接对象，通过连接对象来回滚。
>
>5.如果执行的过程中，没有任何异常情况出现，那么会通过completetransationAfterReturning完成事务的提交，提交的具体逻辑是通过doCommit方法实现，实现的时候也是需要先获取对应连接对象，通过连接对象来回滚。
>
>6.当事务执行完毕的时候，会调用cleanTransationInfo清理相关资源。

## Spring事务传播

>spring事务传播特性有7种 `required`、`requires_new`、`nested`、`support`、`not_support`、`never`、`mandatory`