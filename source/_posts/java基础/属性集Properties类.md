---
title: 属性集Properties类
date: 2019-04-24 17:47:50
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 第六章 属性集

## 6.1 概述

`java.util.Properties ` 继承于` Hashtable` ，来表示一个持久的属性集。它使用键值结构存储数据，每个键及其对应值都是一个字符串。该类也被许多Java类使用，比如获取系统属性时，`System.getProperties` 方法就是返回一个`Properties`对象。

## 6.2 Properties类

### 构造方法

- `public Properties()` :创建一个空的属性列表。

### 基本的存储方法

- `public Object setProperty(String key, String value)` ： 保存一对属性。  
- `public String getProperty(String key) ` ：使用此属性列表中指定的键搜索属性值。
- `public Set<String> stringPropertyNames() ` ：所有键的名称的集合。

```java
public class PropertiesDemo1 {
    public static void main(String[] args) {
        //创建属性集对象
        Properties properties=new Properties();
        //添加键值对元素
        properties.setProperty("filename","a.txt");
        properties.setProperty("length","233333");
        properties.setProperty("location","D:/a.txt");
        //打印属性集对象
        System.out.println(properties);//{filename=a.txt, length=233333, location=D:/a.txt}
        //通过键，获取属性值
        System.out.println(properties.getProperty("filename"));//a.txt
        System.out.println(properties.getProperty("length"));//233333
        System.out.println(properties.getProperty("location"));//D:/a.txt
        //遍历属性集，获取所有键的集合
        Set<String> strings = properties.stringPropertyNames();
        for(String key:strings){
            System.out.println(key+"="+properties.getProperty(key));
        }
        /*
            filename=a.txt
            length=233333
            location=D:/a.txt        
         */
    }
}
```

## 6.3与流相关的方法

`public void load(InputStream inStream)`： 从字节输入流中读取键值对。 

`public void load(Reader reader)`:从字符输入流中读取键值对。 

`public void store(OutputStream out, String comments)`:将此属性列表（键和元素对）写入此 Properties表中，以适合于使用 load(InputStream)方法加载到 Properties表中的格式输出流。

`void store(Writer writer, String comments)`:字符流，以适合使用 load(Reader)方法的格式输出到输出字符流。

```java
public class PropertiesDemo1 {
    public static void main(String[] args) throws IOException {
        //创建属性集对象
        Properties properties=new Properties();
        //添加键值对元素
        properties.setProperty("filename","a.txt");
        properties.setProperty("length","233333");
        properties.setProperty("location","D:/a.txt");

        Writer writer=new FileWriter("a.properties");
        properties.store(writer,"test");
    }
}
```

从文件中加载到properties

```java
public class PropertiesDemo1 {
    public static void main(String[] args) throws IOException {
        Properties properties=new Properties();
        FileReader reader=new FileReader("a.properties");
        properties.load(reader);//加载文件
        Set<String> strings = properties.stringPropertyNames();
        for(String string:strings){
            System.out.println(string+"="+properties.getProperty(string));
        }
    }
}
```

小贴士：文本中的数据，必须是键值对形式，可以使用空格、等号、冒号等符号分隔。