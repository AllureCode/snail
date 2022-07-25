---
title: 设计模式-构建者
cover: https://images.xianhu.wang/20220725.jpg
date:  2022-06-30 16:58:10
updated: 2022-06-30 16:58:10
tags:
  - 设计模式
categories:
  - 设计模式
---

{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
定义：指将一个复杂对象的构造与它的表示分离，使同样的构建过程可以创建不同的表示，这样的设计模式被称为建造者模式。它是将一个复杂的对象分解为多个简单的对象，然后一步一步构建而成。它将变与不变相分离，即产品的组成部分是不变的，但每一部分是可以灵活选择的。
一般使用场景：存在基础模块不会变但是这些模块组合经常发生变化。
优点：
封装性好，构建和表示分离。
扩展性好，各个具体的建造者相互独立，有利于系统的解耦。
缺点：
产品的组成部分必须相同，这限制了其使用范围。
如果产品的内部变化复杂，如果产品内部发生变化，则建造者也要同步修改，后期维护成本较大。
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 示例{% endnote %}

### 使用构建者创建对象
```java
package org.example.builder.objectBuilder;

/**
 * @author xianhu.wang
 * @date 2022年06月30日 4:55 下午
 */
public class People {

    private String name;
    private Integer age;
    private String address;

    private People(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.address = builder.address;
    }

    public static class Builder {
        private String name;
        private Integer age;
        private String address;

        public Builder name(String value) {
            this.name = value;
            return this;
        }

        public Builder age(Integer value) {
            this.age = value;
            return this;
        }

        public Builder address(String value) {
            this.address = value;
            return this;
        }

        public People builder() {
            return new People(this);
        }
    }
    @Override
    public String toString() {
        return "People{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
}

```
```java
// 不使用构建者
  People people = new People();
  people.setAge(10);
  people.setAddress("陕西");
  people.setName("西安");
  System.out.println(people);

// 使用构建者
    People people = new People
                .Builder()
                .address("陕西")
                .name("西安")
                .age(10)
                .builder();
    System.out.println(people);
```
{% note red  'fas -fa-fan' flat%} 
使用构建者创建对象，避免了大量的set，可以支持链式调用，使代码变得更加优雅。
{% endnote %}
