---
title: 接口interface
date: 2019-04-05 11:54:41
tags:	
	- JavaSE
	- Java
categories: JavaSE
---

# 接口代表的是功能

例如USB上的接口，扩展作用。

继承体现的是is a的关系

接口体现的是like a的关系，例如：猴子可以像人一样计算。

譬如游泳这一接口，谁都可以实现。

***在Java中，接口主要用于解决单继承局限的问题***

# 接口特点

 接口是两个模块之间通信的**标准**，通信的规范。如果能把你要设计的模块之间的接口定义好，就相当于完成了系统的设计大纲，剩下的就是添砖加瓦的具体实现了。大家在工作以后，做系统时往往就是使用“面向接口”的思想来设计系统。

***接口的本质是契约，就像我们人间的法律一样。制定好后大家都遵守。***

1. 接口用关键字interface表示

> 格式：interface 接口名 {}

2. 类实现接口用implements表示

> 格式：class 类名 implements 接口名 {}

3. 接口不能实例化

   那么，接口如何实例化呢?

   答：按照多态的方式，由具体的子类实例化，其实这也是多态的一种，接口多态

4. 接口的子类

   抽象类：不必重写所有的抽象方法

   具体类：重写接口中的所有抽象方法

# 成员常量

***只能是常量***

***默认修饰符 public static final***

```java
public class TestDemo {
	public static void main(String[] args) {
		Inter inter=new Demo();
		System.out.println(inter.num1);
		inter.num1=12;	//编译报错The final field Inter.num1 cannot be assigned
	}
}
interface Inter{
	int num1=100;
}
class Demo implements Inter{
}
```



# 构造方法

没有构造方法，不能实例化。

因为接口中所有的成员变量都是静态常量，不能实例化成对象，不需要初始化。

# 成员方法

***接口中所有成员的访问权限都是public***

​	***接口表示的是一个功能，所以谁都可以实现它。***

JDK1.7之前，接口中只能包含静态常量、抽象方法，不能有普通属性、构造方法、普通方法。

​	抽象方法：默认用public abstract修饰

JDK1.8后：接口中包含普通的静态方法和默认方法。

 1. 默认方法：如果该接口的子类没有重写该方法，就是这个默认方法。***这里的default表示的不是访问权限，表示的是默认方法***

    **这里其实是public  default void method()**

    **但是接口中public推荐省略**

```java
 default void method(){
	System.out.println("123");
}
```

​	2. 静态方法：**静态方法只能通过`接口名.静态方法（）`调用，不能通过子类调用**

```java
static void function(){
    System.out.println("123");
}
```

# 接口与类的关系



类与类：继承关系，只能单继承，但是可以多层继承

类与接口：实现关系，也可以多实现，还可以继承一个类的同时实现多个接口

接口与接口：继承关系，可以单继承，也可以多继承

# 抽象类与接口的区别

| 抽象类 | 方法                                                         | 变量                          | 构造方法 | 关系区别                                                  | 设计理念                                          |
| ------ | ------------------------------------------------------------ | ----------------------------- | -------- | --------------------------------------------------------- | ------------------------------------------------- |
| 抽象类 | 抽象方法，普通方法，静态方法                                 | 变量，常量                    | 有       | 类与类（继承），单继承；类与接口 （实现），单实现，多实现 | 抽象类 被继承体现的是：”is a”的关系。**共性功能** |
| 接口   | JDK1.8之前，只能有抽象方法  ；  JDK1.8以后有默认方法（default）、静态方法、抽象方法 | 常量，默认public static final | 无       | 接口与接口（继承），单继承，多继承                        | 接口 被实现体现的是：”like a”的关系。**扩展功能** |

# 接口的实际应用

```java
interface USB{
	default void printInfo() {//默认方法
		System.out.println("采用USB接口连接");
	}
	void start();//抽象方法
	void stop();
	static void usbInfo() {//静态方法
		System.out.println("USB4.0");
	}
}
class Print implements USB{//打印机类
	 public void printInfo() {//覆写默认方法
		System.out.println("打印机类覆写默认方法");
	}

	@Override
	public void start() {
		System.out.println("打印机连接！");
	}
	@Override
	public void stop() {
		System.out.println("打印机断开连接!");
	}	
}
class Flash implements USB{
	@Override
	public void start() {
		System.out.println("U盘连接！");
	}
	@Override
	public void stop() {
		System.out.println("U盘断开连接!");
	}	
}
class Computer{
	public void plugin(USB usb) {
		usb.printInfo();//默认方法
		usb.start();//抽象方法
		usb.stop();//抽象方法
		//usb.usbInfo();//编译错误，接口的静态方法只能通过接口名调用
		USB.usbInfo();
	}
}
public class HW4{
	public static void main(String[] args) {
		Computer computer=new Computer();
		computer.plugin(new Flash());
		System.out.println("--------------");
		computer.plugin(new Print());
	}
}
```

输出结果：

```java
采用USB接口连接
U盘连接！
U盘断开连接!
USB4.0
--------------
打印机类覆写默认方法
打印机连接！
打印机断开连接!
USB4.0
```



