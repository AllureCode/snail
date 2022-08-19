---
title:  高并发-volatile
cover: https://images.xianhu.wang/22060906.jpg
date:  2022-07-25 17:55:10
updated: 2022-07-25 18:00:00
tags:
  - 高并发
categories:
  - 高并发
---

#  高并发-volatile
{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
提起java高并发，不得不说起volatile这个关键字，volatile可以帮助我们写出高效的并发代码，某些情况下比锁更加方便。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 特性-volatile{% endnote %}

{% note red  'fas -fa-fan' flat%} 
1.禁指令重排：通过内存屏障实现
2.可见性：对一个volatile变量的读，总是能看到(任意线程)对这个volatile变量最后的写
入。
3.原子性：对任意单个volatile变量的读/写具有原子性，但类似于volatile++这种复合操作不 具有原子性。
{% endnote %}


{% note red 'fas fa-fan' disabled %} ## 可见性-volatile{% endnote %}

{% note red  'fas -fa-fan' flat%} 
volatile可见性是依赖JMM，被volatile修饰的变量在底层的汇编代码会多一个lock前缀的指令。lock指令在处理器层面会完成一下操作：
1.将当前处理器缓存数据写会到内存
  lock指令会确保数据在写回到内存时候的原子性。也就是“缓存锁定”，在处理器执行期间，会锁住总线，从而避免对该数据进行其他操作。但是锁住总线的很消耗性能，会导致其他cpu不能访问到总线。为了解决这种损耗，lock指令后续采用了锁缓存区，当处理器发现有lock指令前缀的时候，会锁住操作的缓存区，等到操作完成后在释放。从而确保了处理器将值写回内存的时候原子性问题。
2.将原始内存中的值设置为无效状态
  处理器采用嗅探技术保证它的内存内部、系统内存与其他处理器的数据在总线上保存一致。在执行嗅探的处理器会将自己缓存中数据置为无效，下次访问相同的地址时，强制执行缓存行填充。
{% endnote %}


{% note red 'fas fa-fan' disabled %} ## 指令重排-volatile{% endnote %}

{% note green  'fas -fa-fan' flat%} 
xxxxx
{% endnote %}

{%  folding green, xxxx %}
## xxx

{% endfolding %}