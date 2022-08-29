---
title:  ConcurrentHashMap
cover: https://images.xianhu.wang/22060906.jpg
date:  2022-08-24 19:39:10
updated: 2022-07-25 18:00:00
tags:
  - 高并发
categories:
  - 高并发
---

{% note red 'fas fa-fan' disabled %} ## 为什么使用ConcurrentHashMap{% endnote %}

{% note red  'fas -fa-fan' flat%} 
1. HashMap线程不安全,多线程情况下同时对同一个HashMap进行put操作会触发Entry链表形成环形数据结构，导致死循环耗尽cpu。
2. HashTable的效率低下，因为使用到synchronize进行加锁导致效率不好。
3. ConcurrentHashMap的分段锁技术可以提高并发访问率。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## ConcurrentHashMap结构{% endnote %}

{% note red  'fas -fa-fan' flat%} 
{%image https://images.xianhu.wang/2022082501.webp %}

ConcurrentHashMap是由Segment数组结构和HashEntry数组结构组成。Segment是一种可重入锁(ReentrantLock)，在ConcurrentHashMap里扮演锁的角色;HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组。Segment的结构和HashMap类似，是一种数组和链表结构。一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素，每个Segment守护着一个HashEntry数组里的元素，当对HashEntry数组的数据进行修改时， 必须首先获得与它对应的Segment锁。

{% endnote %}


{% note red 'fas fa-fan' disabled %} ## ConcurrentHashMap的操作{% endnote %}

{% note red  'fas -fa-fan' flat%}
## 1. put
   由于put方法里需要对共享变量进行写入操作，所以为了线程安全，在操作共享变量时必须加锁。put方法首先定位到Segment，然后在Segment里进行插入操作。插入操作需要经历两个步骤，第一步判断是否需要对Segment里的HashEntry数组进行扩容，第二步定位添加元素的位置，然后将其放在HashEntry数组里。
   (1)是否需要扩容
   在插入元素前会先判断Segment里的HashEntry数组是否超过容量threshold,如果超过阈值，则对数组进行扩容。值得一提的是，Segment的扩容判断比HashMap更恰当，因为HashMap是在插入元素后判断元素是否已经到达容量的，如果到达了就进行扩容，但是很有可能扩容 之后没有新元素插入，这时HashMap就进行了一次无效的扩容。
   (2)如何扩容   
   在扩容的时候，首先会创建一个容量是原来容量两倍的数组，然后将原数组里的元素进行再散列后插入到新的数组里。为了高效，ConcurrentHashMap不会对整个容器进行扩容，而只对某个segment进行扩容。
## 2. get
   get操作高效的原因是因为不需要加锁，因为使用了volatile变量修饰val。可以保证每次读取都可拿到最新的值，基于volatile可见性（happens-before原则）。
## 3. size
   ConcurrentHashMap获取size是先尝试2次通过不锁住Segment的方式来统计各个Segment大小，如果统计的过程中，容器的count发生了变化，则再采用加锁的方式来统计所有Segment的大小。
   如何判断在统计的时候容器是否发生了变化呢?
   使用modCount变量，在put、remove和clean方法里操作元素前都会将变量modCount进行加1，那么在统计size前后比较modCount是否发生变化，从而得知容器的大小是否发生变化。
{% endnote %}