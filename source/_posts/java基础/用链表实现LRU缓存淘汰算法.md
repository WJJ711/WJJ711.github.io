---
title: 用链表实现LRU缓存淘汰算法
date: 2019-04-12 20:57:09
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 原理

LRU（Least recently used，最近最少使用）算法根据数据的历史访问记录来进行淘汰数据，其核心思想是“如果数据最近被访问过，那么将来被访问的几率也更高”。

# 实现

最常见的实现是使用一个链表保存缓存数据，详细算法实现如下：

LRU（Least Recently Used）:

（缓存大小是n）

链表：

1. 要缓存的数据已经在链表中存在

   > 在链表中将该数据删除，并在头结点处添加该结点

2. 要缓存的数据在链表中不存在

   > a. 链表中空间够用，直接在头结点添加

   >
   >
   >b.链表空间不够用，删除最后一个结点，并且在头结点添加

# 示例

```java
public class LRU {
    private LinkedList cache=null;
    private int size;

    public int getSize() {
        return size;
    }
    //设置缓存大小
    private int defaultLength;

    public void setDefaultLength(int defaultLength) {
        this.defaultLength = defaultLength;
    }
//构造函数
    public LRU(int defaultLength) {
        this.defaultLength = defaultLength;
        cache=new LinkedList();
    }

    public void add(Object e){
      if(cache.contains(e)){
          cache.remove(e);
      }else {
          if(size==defaultLength){
              cache.removeLast();
          }else{
              size++;
          }
      }
        cache.addFirst(e);
    }

    public static void main(String[] args) {
        LRU cache=new LRU(3);
        cache.add("aa");
        cache.add("bb");
        cache.add("cc");
        cache.add("bb");
        cache.add("kk");
        System.out.println(cache);
    }

    @Override
    public String toString() {
        return "LRU{" +
                "cache=" + cache +
                ", size=" + size +
                '}';
    }
}
```

