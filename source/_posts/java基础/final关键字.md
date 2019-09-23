---
title: final关键字
date: 2019-04-04 15:27:05
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# final修饰变量:

被他修饰的变量不可改变。一旦赋了初值，就不能被重新赋值。且必须被赋初值

```java
final  int   MAX_SPEED = 120;
final  int   MAX_SPEED ;//编译出错
```

#  final修饰方法：

该方法不可被子类重写。但是可以被重载!

# final修饰类: 

修饰的类不能被继承。比如：Math、String等。

# final关键字面试题

### final基本类型和引用类型作为参数的情况

1. 基本类型，是值不能被改变

2. 引用类型，是地址值不能被改变，对象内容可以改变