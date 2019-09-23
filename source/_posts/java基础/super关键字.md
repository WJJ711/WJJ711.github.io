---
title: super关键字&&继承树
date: 2019-04-04 10:22:21
tags:
	- Java
	- JavaSE
categories: JavaSE
---

 

this:
		a. 代表当前对象（哪个对象调用这个方法，它就代表哪个对象）
		b.代表该对象的一个空间标识（可以调用在本类中定义的成员）


super： 

​	a: super表示的是该对象内存中的一部分空间，该部分空间存储了从父类中继承的成员，（可以调用在父类中定义的成员）

如图所示，super代表的是里面那个小框，表示的是该对象内存中的一部分空间，该部分空间存储了从父类中继承的成员

{%asset_img super代表的内存空间标识.png%}

***静态方法中不能使用super关键字***

```java
public class TestDemo{
	public static void main(String[] args) {
		Son1 son=new Son1();
		son.method(0);
	}
}
class Father1{
	int n=1024;
}
class Son1 extends Father1{
	int n=2048;
	public void method(int n) {
		System.out.println("n="+n);
		System.out.println("this.n="+this.n);
		System.out.println("super.n="+super.n);						System.out.println("子类对象:"+this);
		//System.out.println(super);编译错误
	}
}
```

输出结果：

```java
n=0//方法中的局部变量
this.n=2048//this代表子类，子类中的成员变量
super.n=1024//上面代码中父类对象没有创建，只new了一个子类对象，所以super代表该对象的空间标识，里面存的是父类继承的成员
子类对象:classlib.Son1@36d64342//相当于this.toString()
```



# 总结

1. super是直接对父类对象的引用。可以通过super来访问父类中被子类覆盖的非static方法或非static属性。

2.  使用super调用普通方法，语句没有位置限制，可以在子类中随便调用。

3. 若是构造方法的第一行代码没有显式的调用super(...)或者this(...);那么Java默认都会调用super(),含义是调用父类的无参数构造方法。这里的super()可以省略。

# 属性/方法的查找顺序：(比如：查找变量h)

1.  查找当前类中有没有属性h（如果是方法，则从当前对象中开始找）
2. 依次上溯每个父类，查看每个父类中是否有h，直到Object
3. 如果没找到，则出现编译错误。
4. 上面步骤，只要找到h变量，则这个过程终止。

# **构造方法调用顺序：**

1. 构造方法第一句总是：super(…)来调用父类对应的构造方法。所以，流程就是：先向上追溯到Object，然后再依次向下执行类的初始化块和构造方法，直到当前子类为止。

2. 子类中的所有构造方法，默认都会访问父类中的无参构造方法

​	为什么呢?

> ***因为子类会继承父类中的数据，可能还会使用父类的数据。所以，子类初始化之前，一定要先完成父类数据的初始化。***

> 每一个构造方法的第一条语句默认都是：super()

3. 子类的构造方法对父类构造方法的调用只能一次

4. 如果父类没有无参构造方法。直接编译会报错，有如下两种解决办法

   ​	a. 调用父类的有参构造方法

   ​	b. 调用本类其它构造方法，其它构造方法必须调用父类的有参构造方法（本质上是要对父类成员进行初始化！）

​      注：静态初始化块调用顺序，与构造方法调用顺序一样，不再重复。

```java
public class TestDemo{
	public static void main(String[] args) {
		new Son2();
		System.out.println("----------------------");
		new Son2(100);
	}
}
class Father2{
	public Father2() {
		System.out.println("Father2()...");
	}
	public Father2(int n) {
		System.out.println("Father2(int)...");
	}
}
class Son2 extends Father2{
	public Son2() {
		super(100);//可以通过super(...)调用父类有参构造方法，此时系统不会默认提供无参构造方法
		System.out.println("Son2()...");
	}
	public Son2(int n) {
        //super();这是编译器自己加的，默认调用父类无参构造方法
		System.out.println("Son2(int)...");
	}
}
```

结果：

```java
Father2(int)...//调用父类有参构造方法
Son2()...
----------------------
Father2()...//调用父类无参构造方法
Son2(int)...
```

# 面试题1

```java
class Father {
	static {
		System.out.println("静态代码块Father");
	}

	{
		System.out.println("构造代码块Father");
	}

	public Father() {
		System.out.println("构造方法Father");
	}
}

class Son extends Father {
	static {
		System.out.println("静态代码块Son");
	}

	{
		System.out.println("构造代码块Son");
	}

	public Son() {
		System.out.println("构造方法Son");
	}
}
public class TestDemo{
	public static void main(String[] args) {
		Son son=new Son();
	}
}
```

结果：

```java
静态代码块Father//1、加载父类的字节码文件(运行静态代码块)
静态代码块Son//2、加载子类的字节码文件
构造代码块Father//3、执行父类的构造代码块（构造代码块相当于构造方法的最前面）
构造方法Father//4、执行父类构造方法
构造代码块Son//5、执行子类的构造代码块
构造方法Son//6、执行子类的构造方法
```

继承对象的初始化过程：

 	1. 加载父类的字节码文件(运行静态代码块)
 	2. 加载子类的字节码文件
 	3. 执行父类的构造代码块（构造代码块相当于构造方法的最前面）
 	4. 执行父类构造方法
 	5. 执行子类的构造代码块
 	6. 执行子类的构造方法

# 面试题2

```java
class X {//2 加载父类X字节码文件
	Y b = new Y();//引用类型的成员变量
        //3 堆中开辟空间，默认初始化b=null，显式初始化b=new Y()，堆中有引用类型成员变量b,接下去执行new Y()
	X() {//6 构造方法初始化 打印X。至此父类X初始化完毕。开始子类Z的初始化
		System.out.println("X");
	}
}
class Y {//4 加载Y类字节码文件，在堆中开辟空间，Y中没有成员变量,所以没有变量初始化
    //5 X中new Y()构造方法初始化，打印Y，退回3
	Y() {//8 Z中new Y(),Y中没有成员变量，构造方法初始化，打印Y
		System.out.println("Y");
	}
}
public class Z extends X {
	Y y = new Y();//7 父类初始化完毕，进行Z的出初始化，堆中开辟空间，默认初始化y=null,显示初始化y=new Y(),接下去去执行new Y();
	Z() {//9 Z中成员变量初始化后，进行Z中构造方法初试化，打印Z
		System.out.println("Z");
	}
	public static void main(String[] args) {//1、程序入口
		new Z(); //2、new Z(),先加载父类X的字节码文件
	}
}
```

```java
Y
X
Y
Z
```

  ## 铺垫的小知识：

  ### 第一个：成员变量有基本类型和引用类型的。

```java
  class Demo {
  		int x = 10;  //基本类型
	  	Student s = new Student();  //引用类型
  }
```

 ###  第二个：类的初始化过程

1. 加载class文件

2.  堆中开辟空间

3.  变量的默认初始化

4.  变量的显示初始化

5.  构造代码块初始化

6.  构造方法初始化



  ### 第三个：遇到extends，就要知道，先初始化父类数据，然后初始化子类数据。

  子类构造方法中的第一行super在这里仅仅表示要先初始化父类数据。

