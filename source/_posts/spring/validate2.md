---
title: Validate校验不生效问题
cover: https://images.xianhu.wang/22053001.jpg
tags:
  - Validate校验 
categories:
  - 程序员的"春天"
---

# Springboot使用Validate校验参数不生效

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
在上一篇中，简单的介绍了springboot中使用validate进行接口参数数据校验。但是，validate也可能会存在校验失效的问题。此篇会针对此类问题进行解决处理。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 现象{% endnote %}
{% image https://images.xianhu.wang/220530.png %}
{% span red, 红色 我们明明每个字段都已经传值，但是接口参数校验却拦截。归根到底是由于我们接口传递的是下划线而代码中DTO接收字段为驼峰。导致参数不匹配，无法校验。%}
{% note red 'fas fa-fan' disabled %} ## 解决方式{% endnote %}

## 第一种 修改对象DTO中参数格式
```java
/**
 * @author xianhu.wang
 * @date 2022/5/25 10:17 下午
 */
@Data
@ToString
public class PeopleDTO {

    @NotNull(message = "用户名称不能为空")
    private String user_name;

    @NotNull(message = "用户地址不能为空")
    private String user_address;

    @Min(value = 18, message = "年龄不得小于18岁")
    private Integer user_age;

}
```

## 第二种 下划线转驼峰
{% p cyan, 请参考下划线转驼峰篇幅 %}