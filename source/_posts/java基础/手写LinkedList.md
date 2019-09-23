---
title: 手写LinkedList
date: 2019-04-13 11:32:31
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# MyLinkedList

其余部分见MyArrayList

```java
package mylinkedlist;

import mylist.MyIterator;
import mylist.MyList;

import java.util.ConcurrentModificationException;
import java.util.NoSuchElementException;

public class MyLinkedList<E> implements MyList<E> {
    private int size;
    private int modCount;
    private Node<E> headMarker;
    private Node<E> endMarker;

    public MyLinkedList() {
        headMarker = new Node<>();
        endMarker = new Node<>(headMarker, null, null);
        headMarker.next = endMarker;
    }

    private static class Node<E> {//定义在外部没有两样所以用静态内部类
        private Node pre;
        private E data;
        private Node next;

        public Node() {
        }

        public Node(Node pre, E data, Node next) {
            this.pre = pre;
            this.data = data;
            this.next = next;
        }
    }

    @Override
    public boolean add(E s) {
        try {

            add(size, s);
        } catch (Exception e) {
            return false;
        }
        return true;
    }

    @Override
    public void add(int index, E s) {
        checkForAdd(index);

        Node<E> orgin = getNode(index);
        Node<E> node = new Node<>(orgin.pre, s, orgin);
        orgin.pre.next = node;
        orgin.pre = node;
        size++;
        modCount++;
    }

    private void checkForAdd(int index) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("index=" + index);
        }
    }

    private Node<E> getNode(int index) {
        if ((index << 2) <= size) {
            Node<E> node = headMarker;
            for (int i = 0; i <= index; i++) {
                node = node.next;
            }
            return node;
        } else {
            Node<E> node = endMarker;
            for (int i = size; i > index; i--) {
                node = node.pre;
            }
            return node;
        }

    }

    @Override
    public void clear() {
        headMarker.next = endMarker;
        endMarker.pre = headMarker;
        size = 0;
        modCount++;
    }

    @Override
    public boolean contains(E s) {
        int index = indexOf(s);
        return index != -1;
    }

    @Override
    public E get(int index) {
        checkIndex(index);
        Node<E> node = getNode(index);
        return node.data;
    }

    private void checkIndex(int index) {
        if (index < 0 || index >= size)
            throw new IndexOutOfBoundsException("index=" + index);
    }

    @Override
    public int indexOf(E s) {
        Node<E> node = headMarker;
        if (s == null) {
            for (int i = 0; i < size; i++) {
                node = node.next;
                if (node.data == null)
                    return i;
            }
        } else {
            for (int i = 0; i < size; i++) {
                node = node.next;
                if (s.equals(node.data)) {
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

    @Override
    public int lastIndexof(E s) {
        Node<E> node = endMarker;
        if (s == null) {
            for (int i = size - 1; i >= 0; i--) {
                node = node.pre;
                if (node.data == null) {
                    return i;
                }
            }
        } else {
            for (int i = size - 1; i >= 0; i--) {
                node = node.pre;
                if (s.equals(node.data)) {
                    return i;
                }
            }
        }
        return -1;
    }

    @Override
    public E remove(int index) {
        checkIndex(index);
        Node<E> node = getNode(index);
        node.pre.next = node.next;
        node.next.pre = node.pre;
        size--;
        modCount++;
        return node.data;
    }

    @Override
    public boolean remove(E s) {
        int index = indexOf(s);
        if (index == -1)
            return false;
        else {
            remove(index);
            return true;
        }
    }

    @Override
    public E set(int index, E s) {
        checkIndex(index);
        Node<E> node = getNode(index);
        E oldValue = node.data;
        node.data = s;
        return oldValue;

    }

    @Override
    public int size() {
        return size;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for(Node<E> node=headMarker.next;node!=endMarker;node=node.next){
            if(node==headMarker.next){
                sb.append(node.data);
            }else {
                sb.append(","+node.data);
            }
        }
        sb.append("]");
        return sb.toString();
    }

    private class Itr implements MyIterator<E> {
        private int cursor;
        private int lastRet = -1;//表示没有上一个访问的元素
        private int expectModCount = modCount;

        @Override
        public void add(E e) {
            MyLinkedList.this.add(cursor, (E) e);
            cursor++;
            expectModCount = modCount;
        }

        @Override
        public boolean hasNext() {
            return nextIndex()<size;
        }

        @Override
        public boolean hasPrevious() {
            return cursor>0;
        }

        private void checkConModication() {
            if (expectModCount != modCount)
                throw new ConcurrentModificationException();
        }

        @Override
        public E next() {
            checkConModication();
            if (!hasNext()) {
                throw new NoSuchElementException();
            }
            lastRet = cursor;
            E next = (E) MyLinkedList.this.get(cursor);
            cursor++;
            return next;

        }

        @Override
        public int nextIndex() {
            return cursor;
        }

        @Override
        public E previous() {
            checkConModication();
            if (!hasPrevious()) {
                throw new NoSuchElementException();
            }
            cursor--;
            lastRet = cursor;
            return (E) MyLinkedList.this.getNode(cursor).data;
        }

        @Override
        public int previousIndex() {
            return cursor - 1;
        }

        @Override
        public void remove() {
            if (lastRet == -1) {
                throw new IllegalStateException();
            }
            MyLinkedList.this.remove(lastRet);
            expectModCount = modCount;
            cursor = lastRet;
            lastRet = -1;

        }

        @Override
        public void set(E e) {
            if (lastRet == -1) {
                throw new IllegalStateException();
            }
            Node<E> node = (Node<E>) MyLinkedList.this.getNode(lastRet);
            node.data = e;
        }

    }

}
```

