---
title: 泛型Generics
date: 2019-04-13 09:33:01
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 概念

​	泛型是JDK1.5以后增加的，它可以帮助我们建立类型安全的集合。在使用了泛型的集合中，遍历时不必进行强制类型转换。JDK提供了支持泛型的编译器，将运行时的类型检查提前到了编译时执行，提高了代码可读性和安全性。

​      泛型的本质就是“数据类型的参数化”。 我们可以把“泛型”理解为数据类型的一个占位符(形式参数)，即告诉编译器，在调用泛型时必须传入实际类型。

# 泛型的好处

A:提高了程序的安全性

B:将运行期遇到的问题转移到了编译期

C:省去了类型强转的麻烦

# 自定义泛型

  我们可以在类的声明处增加泛型列表，如：<T,E,K,V>。

​      此处，字符可以是任何标识符，一般采用这4个字母。



泛型E像一个占位符一样表示“未知的某个数据类型”，我们在真正调用的时候传入这个“数据类型”。

泛型，用来灵活地将数据类型应用到不同的类、方法、接口当中。将数据类型作为参数进行传递。

# 泛型类

›注意:泛型类型必须是引用类型

定义格式：

```
修饰符 class 类名<代表泛型的变量> {  }
```

例如，API中的ArrayList集合：

```java
class ArrayList<E>{ 
    public boolean add(E e){ }

    public E get(int index){ }
   	....
}
```

使用泛型： 即什么时候确定泛型。

**在创建对象的时候确定泛型**

 例如，`ArrayList<String> list = new ArrayList<String>();`

此时，变量E的值就是String类型,那么我们的类型就可以理解为：

```java 
class ArrayList<String>{ 
     public boolean add(String e){ }

     public String get(int index){  }
     ...
}
```

再例如，`ArrayList<Integer> list = new ArrayList<Integer>();`

此时，变量E的值就是Integer类型,那么我们的类型就可以理解为：

```java
class ArrayList<Integer> { 
     public boolean add(Integer e) { }

     public Integer get(int index) {  }
     ...
}
```

# 含有泛型的方法

定义格式：

```
修饰符 <代表泛型的变量> 返回值类型 方法名(参数){  }
```

例如，

```java
class MyGenericMethod {
    public <T> void show(T mvp) {
        System.out.println(mvp);
        System.out.println(mvp.getClass());//返回T的类型
    }
}
```

使用格式：**调用方法时，确定泛型的类型**

```java
public class Test {
    public static void main(String[] args) {
        MyGenericMethod mm = new MyGenericMethod();
        // 演示看方法提示
        mm.show("aaa");
        //输出结果
        //aaa
		//class java.lang.String
        mm.show(123);
         //输出结果
       	// 123
		//class java.lang.Integer
        mm.show(12.45);
         //输出结果
        //12.45
		//class java.lang.Double
    }
}
```

# 含有泛型的接口

定义格式：

```
修饰符 interface接口名<代表泛型的变量> {  }
```

例如，

```java
public interface MyGenericInterface<E>{
	public abstract void add(E e);
	
	public abstract E getE();  
}
```

使用格式：

**1、定义类时确定泛型的类型**

例如

```java
public class MyImp1 implements MyGenericInterface<String> {
	@Override
    public void add(String e) {
        // 省略...
    }

	@Override
	public String getE() {
		return null;
	}
}
```

此时，泛型E的值就是String类型。

 **2、始终不确定泛型的类型，直到创建对象时，确定泛型的类型**

 例如

```java
public class MyImp2<E> implements MyGenericInterface<E> {
	@Override
	public void add(E e) {
       	 // 省略...
	}

	@Override
	public E getE() {
		return null;
	}
}
```

确定泛型：

```java
/*
 * 使用
 */
public class GenericInterface {
    public static void main(String[] args) {
        MyImp2<String>  my = new MyImp2<String>();  
        my.add("aa");
    }
}
```

# 泛型通配符

```java
class Animal {
}

class Dog extends Animal {
}

class Cat extends Animal {
}
```



## 泛型通配符<?>

> 任意类型，如果没有明确，那么就是Object以及任意的Java类了

```java
 Collection<?> c1 = new ArrayList<Animal>();
 Collection<?> c2 = new ArrayList<Dog>();
 Collection<?> c3 = new ArrayList<Cat>();
 Collection<?> c4 = new ArrayList<Object>();//均可编译通过
```



## ? extends E

> 向下限定，E及其子类

```java
Collection<? extends Animal> c5 = new ArrayList<Animal>();
Collection<? extends Animal> c6 = new ArrayList<Dog>();
Collection<? extends Animal> c7 = new ArrayList<Cat>();
Collection<? extends Animal> c8 = new ArrayList<Object>();//报错
```



## ? super E

> 向上限定，E及其父类

```java
  Collection<? super Animal> c9 = new ArrayList<Animal>();
 Collection<? super Animal> c10 = new ArrayList<Dog>();//报错
  Collection<? super Animal> c11 = new ArrayList<Cat>();//报错
 Collection<? super Animal> c12 = new ArrayList<Object>();
```

# Collection接口中的泛型方法

`<T> T[] toArray(T[] a)`

a表示要存储此集合的数组，如果它足够大（大于集合长度），则存储到a

否则将重新分配T类型的新数组


 ```java
public class Demo {

    public static void main(String[] args) {
        List<String> c = new ArrayList<>();
        ((ArrayList<String>) c).add("hello");
        ((ArrayList<String>) c).add("hello");
        ((ArrayList<String>) c).add("hello");
        String[] arr1=new String[3];
        String[] arr2=c.toArray(arr1);
        System.out.println(arr2==arr1);//true。若arr1长度小于c中的长度，则结果为flase
    }
}
 ```

