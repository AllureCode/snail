---
title: Stream流
cover:  https://images.xianhu.wang/0525.jpg
tags: 
  - java
categories:
  - stream
---

# Stream流

>Java 1.8新特性，是对数据处理的一种新方式。分为并行流和串行流。用法：创建流---中间操作----终止流

## 中间操作 

### 筛选和切片

>// 筛选和切片
>//filter 过滤流 接受lambada表达式，从获取的流中排除某些元素
>//limit  截断流  返回limit(n)个元素
>//skip   跳过元素，返回一个扔掉了前n个元素的流 若流中不足n个则返回空流。与limit互补
>//distinct 筛选，通过流所生成的hashCode 和 equals去掉重复的元素。 （需要重写hashCode和equals）

```java

// 1. filter
 public static void test1(){
        List<Person> list = new ArrayList<>();
        list.add(new Person("z",1,"山西"));
        list.add(new Person("zh",2,"山西2"));
        list.add(new Person("zhh",3,"山西3"));
       //输出age不等于2的元素
        list.stream().filter((p)-> p.getAge()!=2).forEach(System.out::println);
    }
}

// 2. limit
public static void test2(){
        List<Person> list = new ArrayList<>();
        list.add(new Person("z",1,"山西"));
        list.add(new Person("zh",2,"山西2"));
        list.add(new Person("zhh",3,"山西3"));
        list.stream().filter((t)-> !"山西".equals(t.getAddress()))
                .limit(1)
                .forEach(System.out::println);
    }

// 3. skip
public static void test3(){
        List<Person> list = new ArrayList<>();
        list.add(new Person("z",1,"山西"));
        list.add(new Person("zh",2,"山西2"));
        list.add(new Person("zhh",3,"山西3"));
        list.stream().filter((t)-> !"山西".equals(t.getAddress()))
                .skip(1)
                .forEach(System.out::println);
    }

//4. distinct
 @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return Objects.equals(name, person.name) &&
                Objects.equals(age, person.age) &&
                Objects.equals(address, person.address);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, address);
    }
public static void test4(){
        List<Person> list = new ArrayList<>();
        list.add(new Person("z",1,"山西"));
        list.add(new Person("zh",2,"山西2"));
        list.add(new Person("zhh",3,"山西3"));
        list.add(new Person("zhh",3,"山西3"));
        list.add(new Person("zhh",3,"山西3"));
        list.stream().filter((t)-> !"山西".equals(t.getAddress()))
                .distinct()
                .forEach(System.out::println);
    }
```

###  映射

> Map  接受lambda，将元素转成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成为一个新的元素。
>
> flatMap---接受一个函数作为参数，将流中的每一个值都转换成另一个流，然后把所有的流连成一个流。

```java
  public static void test5(){
        List<Person> list = new ArrayList<>();
        list.add(new Person("z",12,"山西"));
        list.add(new Person("zh",2,"山西2"));
        list.add(new Person("zhh",3,"山西3"));
        list.add(new Person("zhh",31,"山西3"));
        list.add(new Person("zhh",3,"山西3"));
        list.stream().filter((t)-> !"山西".equals(t.getAddress()))
                .distinct()
                .map((f)->{
                  if (f.getAge()==2){
                      f.setName("黄橙子");
                  }
                   return f;
                })
                .forEach(System.out::println);
    }
```

###  排序

> sorted

```java
  public static void test6() {
        List<Person> list = new ArrayList<>();
        list.add(new Person("z", 12, "山西"));
        list.add(new Person("zh", 2, "山西2"));
        list.add(new Person("zhh", 3, "山西3"));
        list.add(new Person("zhh", 31, "山西3"));
        list.add(new Person("zhh", 3, "山西3"));
    //根据 年龄从小到大排序
        list.stream().sorted(Comparator.comparing(Person::getAge))
        .forEach(System.out::println);
    }

```

## 终止操作

### 查找与匹配

> AllMatch 检查是否匹配所有元素
>
> anyMatch 检查是否至少匹配一个元素
>
> noneMatch 坚持是否没有匹配所有元素
>
> findFirst 返回第一个元素
>
> findAny 返回当前流中的任意元素
>
> count 返回流中元素的总个数
>
> max 返回流中的最大值
>
> min 返回流中的最小值

### reduce 规约 

>reduce 规约 将流中的元素反复结合起来，得到一个新值

### collect 收集 

>collect 收集 返回收集到的数据集合

```java
public static void test7() {
        List<Person> list = new ArrayList<>();
        list.add(new Person("z", 12, "山西",Status.BUY));
        list.add(new Person("zh", 2, "山西2",Status.FREE));
        list.add(new Person("zhh", 3, "山西3",Status.LAZY));

        System.out.println(list.stream().allMatch((t) -> t.getStatus().equals(Status.BUY)));
    }

// reduce
public static void test8() {
        List<Person> list = new ArrayList<>();
        list.add(new Person("z", 12, "山西",Status.BUY));
        list.add(new Person("zh", 2, "山西2",Status.FREE));
        list.add(new Person("zhh", 3, "山西3",Status.LAZY));

        System.out.println(list.stream()
                           .map(Person::getAge)
                           .reduce(0, Integer::sum));
  
        System.out.println(list.stream().map(Person::getAge).reduce(Integer::sum).orElse(0));
    }
// collect
 public static void test9() {
        List<Person> list = new ArrayList<>();
        list.add(new Person("z", 12, "山西", Status.BUY));
        list.add(new Person("zh", 2, "山西2", Status.FREE));
        list.add(new Person("zhh", 3, "山西3", Status.LAZY));

        List<Person> collect = list.stream().filter((t) -> t.getAge() != 2).collect(Collectors.toList());
        for (Person p:collect){
            System.out.println(p);
        }
    }
```

# Optional类

> 解决空指针异常

```java
// Optional 容器的常用方法
// Optional.of(T t) 创建一个Optional实例
// Optional.empty() 创建一个空的Optional实例
// Optional.ofNullable(T t) 若t不为空空则创建t的Optional实例，否则创建一个空实例
// isPresent 判断是否包含空值
// orElse(T t) 如果调用对象为空。则返回t 否则返回原始值
// orElseGet(Supplier s) 如果调用的对象包含值，返回值，否则返回s获取的值
// map(Function f) 如果有值对其进行处理并且返回处理后的Optional，否则返回Optional.empty()
// flatMap(Function f) 与map相似，要求返回值必须为Optional
```

# 时间API

>  LocalDate
>
>  LocalTime
>
>  LocalDateTime
>
>  TemporalAdjuster    时间矫正器 可以获取指定时间的时间
>
>  DateTimeFormatter   格式化时间

```java
// 获取当前的时间 2021-08-08 20:10:51
DateTimeFormatter df =  DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
System.out.println(LocalDateTime.now().format(df));

//获取下个周日此时此刻的时间 2021-08-15 20:10:51
TemporalAdjuster temporalAdjuster = TemporalAdjusters.next(DayOfWeek.SUNDAY);
LocalDateTime dateTime = LocalDateTime.now().with(temporalAdjuster);
System.out.println(dateTime.format(df));
```

# 8锁问题

```java
// 两个同步方法，一个对象调用。乌龟先跑？还是兔子先跑？ 答：乌龟先跑 
// 两个同步方法，两个对象调用。乌龟先跑？还是兔子先跑？ 答：兔子先跑
public class Lock1 {
    public static void main(String[] args) {
        Animal1 animal1=new Animal1();
        new Thread(()->{
            animal1.wuGui();
        }).start();
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(()->{
            animal1.tuZi();
        }).start();
    }
}
// 两个同步方法，一个对象调用。乌龟先跑？还是兔子先跑？ 答：乌龟先跑 
class Animal1{
  //synchronized锁的对象是方法的调用者
    public synchronized void wuGui(){
        System.out.println("乌龟先跑");
    }
    public synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}

// 两个同步方法，两个对象调用。乌龟先跑？还是兔子先跑？ 答：兔子先跑
// synchronized 锁的是方法的调用者，也就是对象锁。两个对象分别调用两个方法持有的是两把把锁，兔子不需要等待。
class Animal1{
    public synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("乌龟先跑");
    }
    public synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}
// 一个同步方法，一个普通方法，一个对象调用，乌龟先跑？还是老虎先跑？ 答：老虎先跑  
// 普通方法没有锁，不需要竞争锁。
class Animal2{
    public synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("乌龟先跑");
    }
    public synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
    public void Laohu(){
        System.out.println("老虎先跑");
    }
}

// 两个同步方法，一个对象调用，乌龟沉睡3秒，乌龟先跑？还是兔子先跑？ 答：乌龟先跑
// synchronized 锁的是方法的调用者，也就是对象锁。两个方法持有的是同一把锁，因此谁先拿到锁谁先执行。
class Animal2{

    public synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("乌龟先跑");
    }
    public synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}

// 两个静态同步方法，一个对象调用，乌龟先跑？还是兔子先跑？ 答：乌龟先跑
// static方法类一加载就执行，synchronized 锁的是Class对象即类所，所以两个方法持有一把锁，谁先得到谁先执行
class Animal3{
    public static synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("乌龟先跑");
    }
    public static synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}
// 两个静态同步方法，两个对象调用，乌龟先跑？还是兔子先跑？ 答：乌龟先跑
// static方法类一加载就执行，synchronized 锁的是Class对象即类所，所以两个方法持有一把锁，谁先得到谁先执行。
class Animal3{

    public static synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("乌龟先跑");
    }
    public static synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}

// 一个静态同步方法，一个普通同步方法，一个对象调用，乌龟先跑？还是兔子先跑？ 答：兔子先跑
// 静态同步方法和普通同步方法分别是类锁和对象锁，相当于两把锁，普通同步方法不要等待。
class Animal4{

    public static synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("乌龟先跑");
    }
    public synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}
// 一个静态同步方法，一个普通同步方法，两个对象调用，乌龟先跑？还是兔子先跑？ 答：兔子先跑
// 静态同步方法和普通同步方法分别是类锁和对象锁，相当于两把锁，普通同步方法不要等待。
class Animal4{

    public static synchronized void wuGui(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("乌龟先跑");
    }
    public synchronized void tuZi(){
        System.out.println("兔子先跑");
    }
}
```