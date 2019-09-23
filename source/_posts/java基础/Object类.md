---
title: Object类
date: 2019-04-08 14:48:17
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# Object类



Object类是所有Java类的根基类，也就意味着所有的Java对象都拥有Object类的属性和方法。如果在类的声明中未使用extends关键字指明其父类，则默认继承Object类。

# getClass()

`public final Class getClass()`

返回对象的运行时类

Class:类类型，用来描述类型的类

​	String getName（）：返回该类的全限定名

**注意事项**

1. 返回的是运行时类
2. 用final修饰，所有对象该方法行为一致(均返回的是运行时类)

**重写equals 若父类和子类不能比较，则用getClass();**

**若要求父类和子类能比较，则要用，instanceof**

```java
 Object s="123";
 System.out.println(s.getClass());//返回实例化对象的类型
 System.out.println(s.getClass().getName());//返回该类的全限定名
```

输出结果：

```java
class java.lang.String
java.lang.String
```

# equals(Object)方法重写的一般方法

1. **`equals(Objcet obj)`,的参数类型一定是Object类型，不然就不是Override覆写而是Overloading重载了！！**

2. 对于一个子类添加了一个新的比较属性，就不能同时满足对称性和传递性。**一般处理就是父类和子类不能比较，即返回false**，使用

   `this.getClass()==obj.getClass()`判断（这也是IDEA自动生成的版本）

3. 对于子类没有添加一个新属性，就可以使用`instanceof`进行判断

# hashCode()

`public int hashCode()`

**概述**：返回该对象的哈希码值

**默认实现：一般是通过将该对象的内存地址转换成一个整数**

**如果重写了equals方法， 也应该重写hashCode**

## **hash算法：**

 	1. 看成一个**模拟随机**映射
 	2. 映射：定义域与值域

hashCode()方法，其实也是一个hash算法

 	1. 定义域：内存地址
 	2. 值域：int的表示范围

特点：同一个对象的hashCode肯定一样，不同对象的hashCode一般来说不一样，但是有极小概率一样

对于随机映射而言，两个不同对象的hashCode一样的概率1/(4e9)；（int范围）

hash算法是加密算法么？

​	hash算法不是加密算法

​	加密算法肯定能解密，hash算法是一个映射函数，不能解密，只有一对一映射才能有反函数，才能解密。

## hashCode的常规协议

1. 一致性：一个对象的值没有改变时，在程序运行期间，不同时刻调用hashCode（）方法，必须返回同一个整数。

2. **如果重写了equals（Object）方法， 也应该重写hashCode**，即用equals(Object)方法比较两个对象相等，那么对这两个对象调用hashCode方法都必须生成相同的整数。

   ​	即把**对象的属性分成关键域、衍生域、无关域**，equals(Object)和hashCode()都应该只根据**关键域的属性**判定

3. 如果根据equals（Object）方法，两个对象不相等，那么对这两个对象中任一对象调用hashCode()方法不要求一定生成不同的hash值。

   但是程序员应该意识到，为不相等的对象生成不同的整数结果可以提高哈希表的性能。

# clone()

`projected Object clone()`

因为是projected修饰，所以（如果在同一个包则可以访问）如果在不同包，只能用子类对象访问，且子类需要实现`Cloneable接口`。

## 深拷贝和浅拷贝

{%asset_img 深拷贝和浅拷贝.png%}

浅拷贝只是将一个对象的所有属性值全部拷贝过来，若有引用数据类型，则拷贝对应的内存地址。故引用数据类型指向的对象是公用的，并没有在堆中生成新的对象。

## Object类默认实现的是浅拷贝。

 （即引用数据类型拷贝的只是地址）

## 如何实现深拷贝

例如对该类进行拷贝

```java
class People implements Cloneable{
    int age;//基本数据类型
    String name;//引用数据类型
    Job job;//引用数据类型
}
```



在重写clone方法中：

1. 实现Cloneable接口

2. 重写clone方法，该方法抛出异常

3. 调用super.clone()方法。完成基本数据类型的拷贝

   ```java
   People peopleClone = (People) super.clone();
   ```

   

4. 对引用数据类型进行拷贝，多层的深拷贝，应该在每个类中重写clone

   ```java
     String nameClone=new String(name);
     Job jobClone= (Job) job.clone();
   ```

5. 完成引用数据类型的拷贝

   ```java
    peopleClone.name=nameClone;
    peopleClone.job=jobClone;
   ```

6. 返回clone

   ```java
   return peopleClone;
   ```



# 一个深拷贝示例

```java
import java.util.Objects;

public class TestDemo{
    public static void main(String[] args) throws CloneNotSupportedException {
        People p=new People(18,"小张",new Job("程序猿",19999));
        People clone= (People) p.clone();
        System.out.println(p==clone);//false
        System.out.println(p.equals(clone));//true
        System.out.println(p.name==clone.name);//false
        System.out.println(p.name.equals(clone.name));//true
        System.out.println(p.job==clone.job);//false
        System.out.println(p.job.equals(clone.job));//true
    }
}
class People implements Cloneable{
    int age;
    String name;
    Job job;

    public People(int age, String name, Job job) {
        this.age = age;
        this.name = name;
        this.job = job;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        People people = (People) o;
        return age == people.age &&
                Objects.equals(name, people.name) &&
                Objects.equals(job, people.job);
    }

    @Override
    public int hashCode() {
        return Objects.hash(age, name, job);
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        People peopleClone = (People) super.clone();
        String nameClone=new String(name);
        Job jobClone= (Job) job.clone();
        peopleClone.name=nameClone;
        peopleClone.job=jobClone;
        return peopleClone;

    }

}
class Job implements Cloneable{
   String work;
   double salary;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Job jobClone= (Job) super.clone();
        String workClone=new String(work);
        jobClone.work=workClone;
        return jobClone;
    }

    public Job(String work, double salary) {
        this.work = work;
        this.salary = salary;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Job job1 = (Job) o;
        return Double.compare(job1.salary, salary) == 0 &&
                Objects.equals(work, job1.work);
    }

    @Override
    public int hashCode() {
        return Objects.hash(work, salary);
    }
}
```



