---
title: 增强for循环与可变参数
date: 2019-04-13 11:32:29
tags:	
	- JavaSE
	- Java
categories: JavaSE
---

# 增强for概述

简化数组和Collection集合的遍历，内部使用迭代器实现

`public interface   Iterable<T> `实现这个接口允许对象成为
"foreach" 语句的目标。

## 格式：

```java
for(元素数据类型 变量 : 数组或者Collection集合) {

  使用变量即可，该变量就是元素
}
```



# 可变参数

定义方法的时候不知道该定义多少个参数

## 格式

```java
修饰符 返回值类型 方法名(数据类型…  变量名){}
```

```java
 public  static int add(int... a){
        int sum=0;
        for(int i:a){
            sum+=i;
        }
        return sum;
 }
  public static void main(String[] args) {
        System.out.println(add(1,2,3));

  }
```





## 注意：

1. 这里的变量其实是一个数组

2. 如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个.

   解释：可变参数的参数个数不一定，编译器自动将传递过来的参数封装成数组，如果像下面这样，则参数全都赋值给了可变参数a。b和c没有进行赋值。所以可变参数a必须放在最后面

   ```java
    public  static int add(int... a,int b,int c){
    }//这样定义是错误的
   ```

   

# Arrays工具类中的一个方法

`public static <T> List<T> asList(T... a)`//其实是一个视图

得到的List集合只能查改，不能增删。

而且List改变，其数组也会改变。

```java
 public static void main(String[] args) {
        String[] arr={"aa","bb","cc"};
        List<String> items=Arrays.asList(arr);//视图技术
        //List<String> items= Arrays.asList("aa","bb","cc");//也可以这样
        System.out.println(items.size());
        //查，可以
        for (String s:items){
            System.out.println(s+"---"+s.length());
        }
        //增，出现异常
       // items.add("dd");//UnsupportedOperationException

        //删除，出现异常
        //items.remove(0);//UnsupportedOperationException

        //改,可以
        items.set(0,"wjj");
        System.out.println(items);//[wjj, bb, cc]
        System.out.println(arr[0]);//wjj,也改变了原数组

    }
```



# 集合和数组的相互转换

集合------->数组

1、`Object[] toArray()`//强烈不推荐！

2、 `<T> T[] toArray(T[] a)`强烈推荐！

数组------->集合

`public static  <T>  List<T> asList(T...  a)`