---
title: 13.注解Annotation
date: 2019-04-30 09:37:45
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 第一章 什么是注解

Annotation是从JDK5.0开始引入的新技术。

## Annotation的作用：

1. 不是程序本身，可以对程序作出解释。(这一点，跟注释没什么区别)
2. 可以被其他程序(比如：编译器等)读取。(注解信息处理流程，是注解和注释的重大区别。如果没有注解信息处理流程，则注解毫无意义)

## Annotation的格式：

* 注解是以“@注释名”在代码中存在的，还可以添加一些参数值，例如：`@SuppressWarnings(value="unchecked")`

## Annotation在哪里使用?

* 可以附加在package, class, method, field等上面，相当于给它们添加了额外的辅助信息，我们可以通过反射机制编程实现对这些元数据的访问。

# 第二章 内置注解

@Override

* – 定义在java.lang.Override中，此注释只适用于修辞方法，表示一个方法声明打算重写超类中的另一个方法声明。

@Deprecated

* 定义在java.lang.Deprecated中，此注释可用于修辞方法、属性、类，表示不鼓励程序员使用这样的元素，通常是因为它很危险或存在更好的选择。

# 第三章 自定义注解

使用@interface自定义注解时，自动继承了`java.lang.annotation.Annotation`接口，由编译程序自动完成其他细节。**在定义注解时，不能继承其他的注解或接口。**

## 要点

* @interface用来声明一个注解

## 格式为

* public @interface 注解名 {定义体}
* 其中的每一个方法实际上是声明了一个配置参数
* 方法的名称就是参数的名称
* 返回值类型就是参数的类型（返回值类型只能是基本类型、Class、String、enum）
* 方法不用写具体实现
* 可以通过default来声明参数的默认值。
* 如果只有一个参数成员，一般参数名为value,表示配置项名称是value，那么在给这个配置项赋值的时候，不用再去写配置的  value =，直接写值即可

## 注意

注解元素必须要有值。我们定义注解元素时，经常使用空字符串、0作为默认值。也经常使用负数(比如：-1)表示不存在的含义

# 第四章 元注解

 元注解的作用就是负责注解其他注解。 Java定义了4个标准的
meta-annotation类型，它们被用来提供对其它 annotation
类型作说明。

* @Target
* @Retention
* @Documented
* @Inherited

**@Retention**：

作用：改变自定义的注解的存活范围。

RetentionPolicy(枚举类型)

1. SOURCE:在源文件中有效（即源文件保留）
2. CLASS:在class文件中有效（即class保留）
3. RUNTIME:在运行时有效（即运行时保留）



**@Target**:

作用，指定该注解能用在什么地方。

ElementType:

* TYPE： 类、接口、枚举、Annotation类型

* METHOD：表示该注解只能用在方法上

* FIELD： 表示该注解只能用在成员上
* CONSTRUCT：用于描述构造器
* LOCAL_VARIABLE: 用于描述局部变量
* PARAMETER: 用于描述参数
* PACKAGE:修饰包

# 第五章 注解 vs 配置文件

|            | 优点：               | 缺点：                           |
| ---------- | -------------------- | -------------------------------- |
| 配置文件： | 可配置，不用改源码。 | 不直观，开发效率低               |
| 注解：     | 直观，开发效率高     | 硬编码，修改之后需要重新编译运行 |

凡是有配置文件的地方都有注解对应

# 第六章 注解和配置文件应用

下面代码只需要修改配置文件或者注解，就能改变对应需求。不需要修改代码

## 配置文件

```java
minNameLength=2
maxNameLength=100
minAge=3
maxAge=200
```

## 名字注解 NameConstraint

```java
package cskaoyan.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * 对名字长度进行限制
 * minLength=2
 * maxLength=100
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface NameConstraint {
    int minNameLength();
    int maxNameLength();
}

```

## 年龄注解 AgeConstraint

```java
package cskaoyan.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * 对年龄进行注解
 * minAge=3
 * maxAge=120
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface AgeConstraint {
    int minAge();
    int maxAge();
}
```

## Student类

```java
package cskaoyan.annotation;

/**
 * pojo类
 * 年龄3-120岁
 * 名字长度2-10
 */
public class Student {
    @NameConstraint(minNameLength = 2,maxNameLength = 9)
    private String name;
    @AgeConstraint(minAge = 3,maxAge = 200)
    private int age;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    private Student() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    private Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

## StudentFactory

```java
package cskaoyan.annotation;

import java.io.FileInputStream;
import java.io.FileReader;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.util.Properties;

/**
 * 工具类
 * 学生工厂
 * 生产学生
 */

public class StudentFactory {
    private StudentFactory() {
    }
    public static Student getStudent(String name,int age) throws Exception{
        /**
         * 通过配置文件
         */
     /*   Properties properties=new Properties();
        properties.load(new FileReader("D:/mycode/ reflection/src/cskaoyan/annotation/app.properties"));
        int minNameLength=Integer.valueOf(properties.getProperty("minNameLength"));
        int maxNameLength=Integer.valueOf(properties.getProperty("maxNameLength"));
        int minAge=Integer.valueOf(properties.getProperty("minAge"));
        int maxAge=Integer.valueOf(properties.getProperty("maxAge"));*/
        //通过反射解析注解
        Class<Student> clazz = Student.class;
        Field nameFiled = clazz.getDeclaredField("name");
        Field ageFiled = clazz.getDeclaredField("age");
        nameFiled.setAccessible(true);
        ageFiled.setAccessible(true);
        int maxNameLength=0;
        int minNameLength=0;
        int maxAge=0;
        int minAge=0;
        if(nameFiled.isAnnotationPresent(NameConstraint.class)){
            NameConstraint annotation = nameFiled.getAnnotation(NameConstraint.class);
            maxNameLength = annotation.maxNameLength();
            minNameLength = annotation.minNameLength();
        }
        if(ageFiled.isAnnotationPresent(AgeConstraint.class)){
            AgeConstraint annotation = ageFiled.getAnnotation(AgeConstraint.class);
            maxAge = annotation.maxAge();
            minAge= annotation.minAge();
        }


        if(name.length()<minNameLength||name.length()>maxNameLength||age<minAge||age>maxAge){
            throw  new IllegalArgumentException("name="+name+",age="+age);
        }
        Constructor<Student> c = clazz.getDeclaredConstructor(String.class, int.class);
        c.setAccessible(true);
        return c.newInstance(name,age);
    }
}
```

## 运行

```java
package cskaoyan.annotation;

import java.lang.reflect.InvocationTargetException;

public class Demo01 {
    public static void main(String[] args) throws Exception{
        Student s1 = StudentFactory.getStudent("wjj", 18);

        System.out.println(s1);
        Student s2 = StudentFactory.getStudent("0123456789", 200);
        System.out.println(s2);
    }
}
```

