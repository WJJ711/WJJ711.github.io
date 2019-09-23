---
title: 手写ArrayList
date: 2019-04-13 11:32:30
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# MyList接口

```java
package mylist;

public interface MyList <E>extends Iterable<E>{
    boolean add(E s);
    void add(int index ,E s);
    void clear();
    boolean contains(E s);
    E get(int index);
    int indexOf(E s);
    boolean isEmpty();
    MyIterator<E> iterator();
    int lastIndexof(E s);
    E remove(int index);
    boolean remove(E s);
    E set(int index,E s);
    int size();
}
```

# MyIterator接口

```java
package mylist;
import java.util.Iterator;

public interface MyIterator<E> extends Iterator<E> {
    void  add(E e);
    boolean hasNext();
    boolean hasPrevious();
    E next();
    int nextIndex();
    E previous();
    int previousIndex();
    void  remove();
    void  set(E e);
}
```

# MyArraylist

```java
package mylist;

import mylist.exception.OutOfMaxSizeException;

import java.util.ConcurrentModificationException;
import java.util.NoSuchElementException;

/**
 * 这是一个简单的顺序线性表
 *
 * @author wjj
 * @version 1.0
 */

public class MyArraylist<E> implements MyList<E>{
    private static final int MAX_CAPACITY = Integer.MAX_VALUE - 8;
    private static final int DEFAULT_CAPACITY = 10;
    private int size;
    private int modCount;//判断并发修改异常
    private Object[] elements;

    public MyArraylist() {
        elements = new Object[DEFAULT_CAPACITY];
    }

    public MyArraylist(int capacity) {
        if (capacity >= 0 && capacity <= MAX_CAPACITY) {
            elements = new Object[capacity];
        } else {
            throw new IllegalArgumentException("capaticy" + capacity);
        }
    }

    /**
     * 在线性表的最后添加元素
     *
     * @param s 要添加的元素
     * @return 操作是否成功
     */

    @Override
    public boolean add(E s) {
        try {
            add(size, s);//代码的复用

        } catch (Exception e) {
            return false;
        }

        return true;
    }

    @Override
    public void add(int index, E s) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("index=" + index);
        }
        int minCapacity = size + 1;
        if (minCapacity > elements.length) {
            int newCapacity = calculate(minCapacity);
            grow(newCapacity);
        }

        System.arraycopy(elements, index, elements, index + 1, size - index);
        elements[index] = s;
        size++;
        modCount++;
    }

    private int calculate(int minCapacity) {
        if (minCapacity > MAX_CAPACITY || minCapacity < 0) {
            throw new OutOfMaxSizeException("minCapacity=" + minCapacity);
        }
        int len = elements.length;
        len = len + (len >> 2);
        if (len > MAX_CAPACITY || len < 0) {
            len = MAX_CAPACITY;
        }
        return len > minCapacity ? len : minCapacity;
    }

    private void grow(int newCapacity) {
        Object[] newArr = new Object[newCapacity];
        System.arraycopy(elements, 0, newArr, 0, elements.length);
        elements = newArr;
    }

    /**
     * 清空线性表中所有元素
     */
    @Override
    public void clear() {
        for (int i = 0; i < size; i++) {
            elements[i] = null;
        }
        modCount++;
        size = 0;
    }

    @Override
    public boolean contains(E s) {
        int index = indexOf(s);
        if (index != -1) {
            return true;
        } else {
            return false;
        }


    }

    @Override
    public E get(int index) {
        check(index);
        return (E) elements[index];
    }

    private void check(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("index=" + index);
        }
    }

    @Override
    public int indexOf(E s) {
        if (s == null) {
            for (int i = 0; i < size; i++) {
                if (((E) elements[i]) == null) {
                    return i;
                }
            }
        } else {
            for (int i = 0; i < size; i++) {
                if (s.equals((E) elements[i])) {
                    return i;
                }
            }

        }
        return -1;
    }

    @Override
    public boolean isEmpty() {
        return size == 0;
    }

    @Override
    public MyIterator iterator() {
        return new Itr();
    }

//内部类，iterator()为内部类对象

    private class Itr implements MyIterator<E>{

        private int cursor;//表示下一个元素的索引位置比如cursor=0，表示指针在0前面
        private int expectModCount;//跟外面的modcount比较
        private int ret=-1;//返回元素的索引值,-1表示还没有返回值

        public Itr() {
            expectModCount=modCount;
        }

        /**
         * 添加一个元素，在cursor的位置
         * @param o
         */
        @Override
        public void add(E o) {
            MyArraylist.this.add(cursor,(E) o);
            ret=cursor;
            cursor++;
            expectModCount=modCount;
        }

        @Override
        public boolean hasNext() {
            /*if(cursor==size)
                return false;
            else return true;*/
            return cursor!=size;
        }

        @Override
        public boolean hasPrevious() {
            if(cursor>0)
                return true;
            else
                return false;
        }

        /**
         * 指针后移，并返回越过的元素值
         * @return
         */

        @Override
        public E next() {
            checkConModification();
            if(!hasNext()){
                throw new NoSuchElementException();
            }
            ret=cursor;

            return (E) elements[cursor++];
        }
        private void checkConModification(){
            if(expectModCount!=modCount){
                throw new ConcurrentModificationException();
            }
        }

        @Override
        public int nextIndex() {
            return cursor;
        }

        @Override
        public E previous() {
            checkConModification();
            if(!hasPrevious()){
                throw new NoSuchElementException();
            }
            ret=cursor-1;
            return (E)elements[--cursor];
        }

        @Override
        public int previousIndex() {
            return cursor-1;
        }

        @Override
        public void remove() {
            if(ret==-1){
                throw new IllegalStateException("ret="+ret);
            }
            else{
                cursor=ret;
                MyArraylist.this.remove(ret);
                expectModCount=modCount;
                ret=-1;
            }
        }

        @Override
        public void set(Object o) {
            if(ret==-1){
                throw new IllegalStateException("ret="+ret);
            }
            MyArraylist.this.set(ret,(E)o);

        }
    }
    @Override
    public int lastIndexof(E s) {
        if (s == null) {

            for (int i = size - 1; i >= 0; i--) {
                if (((E) elements[i]) == null) {
                    return i;
                }
            }
        } else {
            for (int i = size - 1; i >= 0; i--) {
                if (s.equals(((E) elements[i]))) {
                    return i;
                }
            }
        }
        return -1;
    }

    @Override
    public E remove(int index) {
        check(index);
        E element = (E) elements[index];
        System.arraycopy(elements, index + 1, elements, index, elements.length - index - 1);
        size--;
        modCount++;
        return element;
    }

    @Override
    public boolean remove(E s) {
        int index = indexOf(s);
        if (index == -1)
            return false;
        else
            remove(index);
        return true;
    }

    @Override
    public E set(int index, E s) {
        check(index);
        E OldValue = ((E) elements[index]);
        elements[index]=s;

        return OldValue;
    }

    @Override
    public int size() {
        return size;
    }
    //属性


    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for (int i = 0; i < size; i++) {
            if (i != size - 1) {

                sb.append(elements[i] + ",");
            }
            else {
                sb.append(elements[i]);
            }
        }
        sb.append("]");
        return sb.toString();
    }
}
```

# 自定义异常

```java
package mylist.exception;

public class OutOfMaxSizeException extends RuntimeException {
    public OutOfMaxSizeException() {
    }

    public OutOfMaxSizeException(String message) {
        super(message);
    }
}
```

