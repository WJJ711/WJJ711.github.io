---
title: Collection接口
date: 2019-04-11 17:03:18
tags: 
	- JavaSE
	- Java
categories: JavaSE
---

# 容器的接口层次结构图

{%asset_img 容器的接口层次结构图.png%}

#  **Collection接口中定义的方法**

增减的操作返回值：原集合发生改变则返回true，否则返回false

  Collection 表示一组对象，它是集中、收集的意思。Collection接口的两个子接口是List、Set接口。

  由于List、Set是Collection的子接口，意味着所有List、Set的实现类都有上面的方法。我们下一节中，通过ArrayList实现类来测试上面的方法。

Collection是所有单列集合的父接口，因此在Collection中定义了单列集合(List和Set)通用的一些方法，这些方法可用于操作所有的单列集合。方法如下：

{%asset_img Collection接口中定义的方法.png%}

# 对集合遍历

1. Object[] toArray()

   把集合转成数组，可以实现集合的遍历

   但是**对转变后的数组改变，不会对原集合造成影响**

2. Iterator iterator()

   迭代器，集合的专用遍历方式

# 迭代器Iterator()

## 迭代器实现原理

**在调用Iterator的next方法之前，迭代器的索引位于第一个元素之前，不指向任何元素**，当第一次调用迭代器的next方法后，迭代器的索引会向后移动一位，指向第一个元素并将该元素返回，当再次调用next方法时，迭代器的索引会指向第二个元素并将该元素返回，依此类推，直到hasNext方法返回false，表示到达了集合的末尾，终止对元素的遍历。

{%asset_img 迭代器的实现原理.bmp%}

## 特别注意

**用迭代器迭代的时候，如果要对集合进行修改，必须要用迭代器修改，不能用集合修改！**

为什么Iterator要设置成一个接口，而不是一个类？

答：因为有一些集合，是可以存储重复数据的，有些集合不能存储重复数据。有些集合是有序的，有些集合是无序的。原因是具体的集合子类底层实现的数据结构不一样。所以不能使用单独的类来实现对所有集合的遍历，所以应该设置成抽象类或者接口

那为什么不设置成抽象类，而要设置成接口呢？

答：因为很多集合可能已经继承了其它集合类，就不能再继承这个抽象类。所以才将Iterator设置成一个接口。

那应该怎么去实现Iterator接口？

答：`Iterator iter=c.iterator();`

具体子类对象应该清楚该集合的数据结构。一个类清楚另外一个类的数据结构，那它应该设计出一个内部类。**即该集合类的内部类实现Iterator接口。**

在ArrayList中实现如下：

```java
private class Itr implements Iterator<E> {
    //重写Iterator接口中的hasNext()，next() remove() 
}
public Iterator<E> iterator() {
        return new Itr();//重写Iterable接口中的iterator（）方法
}
```



