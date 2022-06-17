---
title: SpringBoot-自定义注解
cover: https://images.xianhu.wang/2022061701.jpg
date:  2022-06-17 09:43:10
updated: 2022-06-17 09:43:10
tags:
  - SpringBoot
categories:
  - 程序员的"春天"
---

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
在开发中，我们会经常使用注解来完善我们系统的功能。例如使用注解进行接口层面的监控打点、日志记录等等，今天给大家分享一下如何自定义我们的注解。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 自定义注解{% endnote %}

### 定义注解
```java
/** 用于超时打点监控
 * @author xianhu.wang
 * @date 2022/6/16 2:17 下午
 */
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface MetricsMethod {
    int timeout() default 0;
}
```
### 实现注解

```java
/**
 * @author xianhu.wang
 * @date 2022/6/16 4:07 下午
 */
@Component
@Aspect
public class MetricsMethodMonitor {

    private static Logger logger = LoggerFactory.getLogger(MetricsMethodMonitor.class);
 
    // 声明注解切入点
    @Pointcut("@annotation(cn.xxx.common.annotation.MetricsMethod)")
    private void cut() {
    }

    // 环绕通知
    @Around("cut()")
    public Object MetricsMethod(ProceedingJoinPoint point) {
      // 获取到注解参数
      MethodSignature signature = (MethodSignature) point.getSignature();
      Method method = signature.getMethod();
      MetricsMethod annotation = method.getAnnotation(MetricsMethod.class);
      int timeout = annotation.timeout();
      // 获取对应方法入参的索引
      int index = ArrayUtils.indexOf(signature.getParameterNames(), MonitorConstant.NAME);
      // 获取到对应的索引值（如果存在的话）
      if (index != -1) {
        String name = (String) point.getArgs()[index];
       }
      long start = System.currentTimeMillis();
       try {
        // 执行目标方法
         return point.proceed();
        } finally {
         long interval = System.currentTimeMillis() - start;
          // 超时打点
          ....
        }
    }
```

### 注解使用
```java 
    @MetricsMethod(timeout = INVOKE_ALL_TIMEOUT)
    public List<Name> searchList(String name){
        ....
    }
```

{% note red 'fas fa-fan' disabled %} ## 总结{% endnote %}

{% note green  'fas -fa-fan' flat%} 
注解知识点
{% endnote %}

{%  folding green, 点击查看 %}

## 元注解（@Target、@Retention、@Inherited、@Documented）
### @Target：用于描述注解的使用范围

| **Target类型**              | **描述**                             |
| --------------------------- | ------------------------------------ |
| ElementType.TYPE            | 应用于类、接口（包括注解类型）、枚举 |
| ElementType.FIELD           | 应用于属性（包括枚举中的常量）       |
| ElementType.METHOD          | 应用于方法                           |
| ElementType.PARAMETER       | 应用于方法的形参                     |
| ElementType.CONSTRUCTOR     | 应用于构造函数                       |
| ElementType.LOCAL_VARIABLE  | 应用于局部变量                       |
| ElementType.ANNOTATION_TYPE | 应用于注解类型                       |
| ElementType.PACKAGE         | 应用于包                             |

### @Retention：表明该注解的生命周期


| **生命周期类型**        | **描述**                                         |
| ----------------------- | ------------------------------------------------ |
| RetentionPolicy.RUNTIME | 由JVM 加载，包含在类文件中，在运行时可以被获取到 |
| RetentionPolicy.CLASS   | JVM加载时被丢弃，包含在类文件中，默认值          |
| RetentionPolicy.SOURCE  | 编译时被丢弃，不包含在类文件中                   |

### @Inherited：是一个标记注解
@Inherited阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。

### @Documented
表明该注解标记的元素可以被Javadoc 或类似的工具文档化

{% endfolding %}