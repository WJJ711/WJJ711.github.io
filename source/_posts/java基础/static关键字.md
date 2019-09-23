---
title: static关键字
date: 2019-04-02 17:08:08
tags:
	- Java
	- JavaSE
categories: JavaSE
---

# 核心要点

1. **static修饰的成员变量和方法，从属于类。**
2. **普通变量和方法从属于对象的。**

　在类中，用static声明的成员变量为静态成员变量，也称为类变量。 类变量的生命周期和类相同，在整个应用程序执行期间都有效。它有如下特点：

 　1. 为该类的**公用变量，属于类**，被该类的所有实例共享，在类被载入时被显式初始化。***（随着类的加载而加载）***
 　2. **优先于对象存在**
 　3. 对于该类的所有对象来说，static成员变量只有一份。***被该类的所有对象共享!!***
 　4. 一般用**“类名.类属性/方法”**来调用。(也可以通过对象引用或类名(不需要实例化)访问静态成员。**【强烈推荐使用类名调用！！！】**
 　5. 在static方法中不可直接访问非static的成员。**（因为非static成员要对象存在才存在，而static方法优先于对象存在，从属于类）**
 　6. this不能用于static方法中。（因为**this的本质就是“创建好的对象的地址”!** ）

# 内存分析

```java
public class TestDemo{
	public static void main(String[] args) {
		Person p1=new Person("中国","迪丽热巴",20);
		p1.show();
		Person p2=new Person("中国","古力娜扎",21);
		p2.show();
		Person p3=new Person("中国","凤姐",40);
		p3.show();
		System.out.println("----------------------------");
		p3.nation="America";
		//等价于Person.nation="America";
		p1.show();
		p2.show();
		p3.show();
	}
}
class Person{
	static String nation;
	String name;
	int age;
	public Person() {//构造方法
	}
	public Person(String nation,String name,int age) {
		this.name=name;
		this.nation=nation;
		this.age=age;
	}
	public void show() {
		System.out.println("nation="+nation+",name="+name+",age="+age);
	}
}
```

输出结果：

```java
nation=中国,name=迪丽热巴,age=20
nation=中国,name=古力娜扎,age=21
nation=中国,name=凤姐,age=40
----------------------------
nation=America,name=迪丽热巴,age=20
nation=America,name=古力娜扎,age=21
nation=America,name=凤姐,age=40
```

因为nation为静态属性，为类共享，所以全改了

{%asset_img static内存图解.png%}

# main方法：

`public static void main(String[] args){}`

public :公共的，main方法是被JVM主动调用的，所以访问权限得最大

static:静态的，在被调用之前，根本就没有对象存在，所以应该用static修饰

void:返回给调用者，main方法的调用者是JVM，把值返回给JVM是没有意义的，所以用void

main:只是一个约定俗成的名字

String[] args: JDK1.5 以前没有Scanner类，String[] args用于接受键盘录入的数据的,在dos窗口运行HelloWorld.class文件时，需要执行语句

```java
class HelloWorld{
    public static void main(String[] args){
		for(int i=0;i<args.length;i++){
			System.out.println(args[i]);
		}
	}
}
```



```java
java HelloWorld hello world hangzhou
```



"hello","world","hangzhou"为我输入的3个字符串分别为args[0],args[1],args[2]







