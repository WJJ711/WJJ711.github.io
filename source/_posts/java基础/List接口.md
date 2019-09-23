---
title: List接口
date: 2019-04-12 11:01:23
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# List接口

List是有序、可重复的容器。

​      **有序：**List中每个元素都有索引标记。可以根据元素的索引标记(在List中的位置)访问元素，从而精确控制这些元素。

​      **可重复：**List允许加入重复的元素。更确切地讲，List通常允许满足 e1.equals(e2) 的元素重复加入容器。

# Interface ListIterator<E>

- 用于允许程序员沿任一方向遍历列表的迭代器，在迭代期间修改列表，并获取列表中迭代器的当前位置。
- 其*光标位置* 始终位于通过调用`previous()`返回的元素和通过调用`next()`返回的元素`next()`  。  长度为`n`的列表的迭代器具有`n+1`可能的光标位置（位置为元素之间）

```java
public class Test {
    public static void main(String[] args) {

        List<String> list=new ArrayList<>();
        list.add("hello");
        list.add("world");
        list.add("java");
        ListIterator it=list.listIterator();
        while (it.hasNext()){
            String s = (String) it.next();
            System.out.println(s+"---"+ s.length());
        }
        System.out.println("------------");
        while (it.hasPrevious()){
            String s = (String) it.previous();
            System.out.println(s+"----"+s.length());
        }
        System.out.println(it.previousIndex());//-1
        System.out.println(it.nextIndex());//0
    }
}
```

**previous和next位置只差了1，it指向两元素中间**