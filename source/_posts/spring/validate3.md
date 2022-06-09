---
title: SpringBoot使用下划线转驼峰后导致Validate校验不生效
cover: https://images.xianhu.wang/22053001.jpg
date:  2022-05-31 19:20:10
updated: 2022-05-31 19:20:10
tags:
  - Validate校验 
categories:
  - 程序员的"春天"
---

# SpringBoot使用下划线转驼峰后导致Validate校验不生效

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
当我们使用前一篇中下划线转驼峰的方式后，我们会神奇的发现springboot中validate突然不生效，有因必有果，正如世上没有无缘无故的爱也没有无缘无故的恨。那么，请仔细阅读这篇文章，相信我，它肯定不会浪费你的时间～～
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 现象{% endnote %}

{% tip %}
使用了下划线转驼峰、突然发现，欧吼，validate怎么不生效，看官不要慌。请小的给你分析一波～
{% endtip %}

{% note red 'fas fa-fan' disabled %} ## 分析过程{% endnote %}
## debug源码