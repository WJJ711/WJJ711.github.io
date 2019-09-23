---
title: String和StringBuffer
date: 2019-04-09 11:07:26
tags:
	- Java
	- JavaSE
categories: JavaSE
---

# 概述

 String类又称作不可变字符序列。对于不可变对象，本身就是线程安全的，不需要同步机制去保证。

# String面试题

1.

> String s = new String(“hello”);和String s = “hello”;的区别?

String s = new String(“hello”);创建了两个对象，常量池中的"hello"，和堆内存中的对象

String s = “hello”;只创建了一个对象

2.

```java
String s1 = "hello";
String s2 = "world";
String s3 = "helloworld";
System.out.println(s3==(s1+s2));//false
System.out.println(s3.equals(s1+s2));//true
		
System.out.println(s3==("hello"+"world"));
//true"hello"和"world"在编译时会自动拼接并放入常量池。所以常量池中有"helloworld"
System.out.println(s3.equals("hello"+"world"));//true
```

# String 与其它类型数组转换功能

##　byte[] 

```java
String s="abc";
byte[] b=s.getBytes();//[97, 98, 99]
byte[] m={97, 98, 99};
String str=new String(m);//abc
```

## char[]

```java
 String s="abc";
 char[] c=s.toCharArray();//[a, b, c]
 System.out.println(Arrays.toString(c));
 char[] m={'a', 'b', 'c'};
 String str=new String(m);//abc
 System.out.println(str);
```



# StringBuffer

线程安全的可变字符序列

## 添加功能

```java
public StringBuffer append(String str);

public StringBuffer insert(int offset,String str);
```



## 删除功能

```java
public StringBuffer deleteCharAt(int index);

public StringBuffer delete(int start,int end);
```

## 替换功能

```java
public StringBuffer replace(int start,int end,String str);
```



## 反转功能  

```java
public StringBuffer reverse();
```

## 截取功能

```java
public String substring(int start)
public String substring(int start,int end)
```

***注意***：截取功能和前面几个功能的不同

返回值类型是String类型，本身没有发生改变

# StringBuilder

一个可变的字符序列。此类提供一个与 `StringBuffer` 兼容的 API，但不保证同步。该类被设计用作 `StringBuffer` 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。**如果可能，建议优先采用该类，因为在大多数实现中，它比 `StringBuffer` 要快**

# 面试题

```java
package casual;

public class Test {
    public static void main(String[] args) {
        String s1="hello";
        String s2="world";
        change(s1,s2);
        System.out.println("s1 = " + s1);//s1= hello
        System.out.println("s2 = " + s2);//s2= world
        System.out.println("--------------");
        StringBuilder sb1=new StringBuilder("hello");
        StringBuilder sb2=new StringBuilder("world");
        change(sb1,sb2);
        System.out.println("sb1 = " + sb1);// sb1 = hello
        System.out.println("sb2 = " + sb2);// sb2 = worldworld
    }

   public static void change(StringBuilder sb1,StringBuilder sb2){
        sb1=sb2;
        sb2=sb2.append(sb1);
   }
    public static void change(String s1,String s2){
        s1=s2;
        s2+=s1;
    }
}

```

# 结论

对于不可变对象，他们作为参数的时候，跟基本数据类型作为参数保持一致，相当于C中的基本数据类型传递

对于可变对象，他们作为参数的时候，跟引用数据类型作为参数保持一致。






